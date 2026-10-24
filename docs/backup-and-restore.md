# Backup and restore

The live instance uses SQLite inside the Podman volume `n8n-lab-data`. Git does not contain this state. Backups can contain credentials, execution data, and private workflow content, so keep them outside the repository with restricted permissions.

Keep the matching encryption key stable. This procedure preserves `.env` as a separate file, outside the data archive. Store a secure encrypted/off-machine copy of the key separately too; a backup on this Mac does not protect against loss of the Mac. Do not upload these files to the public repository.

Run the commands from the repository root. Shell-exported secret variables override environment files; do not export them when operating or restoring this lab.

## Consistent stopped-instance backup

Stop both services before exporting SQLite. Run this block as a whole; it aborts on errors and leaves the services stopped if a backup step fails.

```sh
(
  set -eu
  umask 077
  podman compose stop
  if [ -n "$(podman ps -q --filter label=com.docker.compose.project=n8n-lab)" ]; then
    echo 'Lab containers are still running; do not export SQLite.' >&2
    exit 1
  fi

  backup_dir="$HOME/.local/share/n8n-lab/backups/$(date -u +%Y%m%dT%H%M%SZ)"
  mkdir -p "$HOME/.local/share/n8n-lab/backups"
  mkdir -m 700 "$backup_dir"
  podman volume export n8n-lab-data --output "$backup_dir/n8n-data.tar"
  cp .env "$backup_dir/environment.env"
  chmod 600 "$backup_dir/n8n-data.tar" "$backup_dir/environment.env"
  echo "Private backup: $backup_dir"
  podman compose up -d
)
```

A timestamp collision causes the directory creation to fail instead of overwriting an older backup. Record the current Git commit and both image versions alongside your private backup. A successful export alone is not proof of recoverability: rehearse the restore below.

If an operation fails, inspect the error and service state before restarting. Never export while a process is still writing SQLite. Use `podman compose ps` and `curl --fail http://localhost:5678/healthz/readiness` after restarting.

## Rehearse restoration without touching the working instance

Choose an existing backup directory and export it as `N8N_LAB_BACKUP_DIR` for this one check. The following Python block creates a new temporary volume, imports the backup, exports it again, and compares every entry's type, mode, ownership, size, and file checksum or link target. It ignores tar ordering and timestamps. It reads the archives directly, without extracting private contents to the host filesystem.

```sh
export N8N_LAB_BACKUP_DIR="$HOME/.local/share/n8n-lab/backups/REPLACE_WITH_BACKUP_TIMESTAMP"
python3 - <<'PYTHON'
import hashlib
import os
from pathlib import Path
import subprocess
import tarfile
import tempfile
import uuid

backup = Path(os.environ['N8N_LAB_BACKUP_DIR'])
archive = backup / 'n8n-data.tar'
if not archive.is_file():
    raise FileNotFoundError(archive)

def run(*args):
    subprocess.run(args, check=True, stdout=subprocess.DEVNULL)

def manifest(path):
    entries = {}
    with tarfile.open(path) as source:
        for member in source.getmembers():
            name = member.name.removeprefix('./').rstrip('/')
            if name in ('', '.'):
                continue
            digest = (hashlib.file_digest(source.extractfile(member), 'sha256').hexdigest()
                      if member.isfile() else member.linkname)
            entries[name] = (member.type, member.mode, member.uid, member.gid,
                             member.size, digest)
    return entries

volume = 'n8n-lab-restore-check-' + uuid.uuid4().hex[:12]
run('podman', 'volume', 'create', volume)
try:
    run('podman', 'volume', 'import', volume, str(archive))
    with tempfile.TemporaryDirectory(prefix='n8n-restore-') as temporary:
        restored = Path(temporary) / 'restored.tar'
        run('podman', 'volume', 'export', volume, '--output', str(restored))
        if manifest(archive) != manifest(restored):
            raise RuntimeError('Restored contents differ from backup')
    print('PASS: restored file contents and permissions match the backup')
finally:
    # Only the uniquely created rehearsal volume is removed.
    run('podman', 'volume', 'rm', volume)
PYTHON
unset N8N_LAB_BACKUP_DIR
```

This checksum helper requires Python 3.11 or later (`hashlib.file_digest`). The live volume and services are untouched during this rehearsal. Temporary-directory permissions protect the comparison archive. A cleanup failure is reported; inspect the exact temporary volume before removing it manually.

This verifies archive restoration. It does not test every workflow against external providers. After you add workflows, also verify representative workflows and credential use during a separate recovery exercise.

## Recover an instance into a new volume

1. Stop the working stack with `podman compose stop`. Keep the original volume intact. Select the backup, its matching `environment.env`, and the Git revision/image versions used to produce it.
2. Create a distinct volume, for example `n8n-lab-recovered-20261024`, then import the selected archive into it:

   ```sh
   podman volume create n8n-lab-recovered-20261024
   podman volume import n8n-lab-recovered-20261024 /absolute/private/backup/n8n-data.tar
   ```

   Confirm the chosen volume name did not already exist before importing. Do not substitute `n8n-lab-data`.

3. Create a private Compose override outside Git, containing:

   ```yaml
   volumes:
     n8n-data:
       name: n8n-lab-recovered-20261024
       external: true
   ```

4. Use the backup's matching environment file and this override for **every** recovery command:

   ```sh
   podman compose --env-file /absolute/private/backup/environment.env \
     -f compose.yaml -f /absolute/private/recovery.compose.yaml up -d
   podman compose --env-file /absolute/private/backup/environment.env \
     -f compose.yaml -f /absolute/private/recovery.compose.yaml ps
   curl --fail http://localhost:5678/healthz/readiness
   ```

5. Verify the owner login, workflows, and credentials. The encryption key must match the backed-up instance. Keep the original volume and backups until recovery is confirmed. Do not casually mix the recovery commands with normal commands, which target the original volume again.

Do not import archives over the working volume. Avoid `down --volumes` and volume pruning. If upgrading, back up first and update both images together; rolling back image tags alone may not undo database migrations.
