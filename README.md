# n8n-lab

A local n8n playground on macOS ARM64, using Podman. This repository versions runtime configuration and operating documentation; reviewed workflow examples live in [n8n-workflows](https://github.com/Light2288/n8n-workflows/tree/main). Both official images are pinned to **2.44.6**. Docker is not required.

The editor runs at **http://localhost:5678**. SQLite and instance settings live in the named volume `n8n-lab-data`. An external task-runner container executes Code nodes without receiving that volume or the n8n encryption key. The container network permits outbound API requests; it is not an air-gapped environment.

## First setup

Prerequisites: Git, Python 3, a running Podman machine, and a Podman Compose provider. This setup was verified with Podman 6.1.2 and podman-compose 1.6.0. Installations without a Compose provider must configure one; Docker Compose is unnecessary.

```sh
podman machine list
podman machine start  # only if your machine is stopped
podman compose version
```

Clone the runtime configuration from `main` and enter its directory:

```sh
git clone --branch main https://github.com/Light2288/n8n-lab.git
cd n8n-lab
```

Generate a new `.env` once, from the repository root. Exclusive creation prevents accidental replacement of the key used to encrypt stored credentials. The file has mode `0600`; values are not printed.

```sh
python3 - <<'PYTHON'
import os
import secrets

fd = os.open('.env', os.O_WRONLY | os.O_CREAT | os.O_EXCL, 0o600)
with os.fdopen(fd, 'w') as output:
    output.write('N8N_ENCRYPTION_KEY=' + secrets.token_hex(32) + '\n')
    output.write('N8N_RUNNERS_AUTH_TOKEN=' + secrets.token_hex(32) + '\n')
PYTHON

git check-ignore .env
podman compose up -d
podman compose ps
curl --fail http://localhost:5678/healthz/readiness
```

Wait for readiness to return HTTP 200, then open http://localhost:5678 and create your owner account in the browser. The account is local to this instance; it is separate from an n8n Cloud account. Keep the encryption key stable and back it up securely before adding credentials. Do not run a new key-generation command when recovering an existing instance.

The empty values in `.env.example` are documentation, not usable defaults. Compose rejects missing or empty secrets before startup. Avoid exporting the two secret variables in your shell: shell variables take precedence over `.env`.

## Daily operation

Run these commands from the repository root, with the Podman VM running:

```sh
podman compose up -d                 # start or recreate services
podman compose ps                    # service state and readiness
podman compose logs --tail=100 n8n task-runners
podman compose stop                  # stop services; keep containers and data
podman compose start                # restart existing stopped containers
curl --fail http://localhost:5678/healthz/readiness
podman stats --no-stream             # current resource use
podman compose down                  # remove containers/network; keep named volume
```

Use `up -d` for configuration/image changes. A normal `down` preserves the named volume. **Do not use `down --volumes`, delete `n8n-lab-data`, or prune volumes containing this instance.** Those operations can destroy workflows, accounts, credentials, and settings.

Only `127.0.0.1:5678` is published. The broker at `n8n:5679` and runner health port 5680 stay inside the container network. HTTP requires `N8N_SECURE_COOKIE=false` here. Before public exposure, design TLS, authentication/access controls, cookie settings, URLs, and webhook networking for that deployment.

Workflows stop running when macOS sleeps or the Podman VM is stopped. Container restart policies do not start a stopped VM. This is a personal lab, not an always-on service. The VM initially retains 2 GiB RAM; check resource use as workflows grow.

## Workflows and recovery

This repository stores runtime configuration and documentation. [n8n-workflows](https://github.com/Light2288/n8n-workflows/tree/main) owns reviewed workflow JSON, examples, and the manual export/review/import procedure.

Saving in the editor updates the local database. Committing or pulling exported JSON does not synchronize that database; import definitions explicitly and configure credentials locally. See the [workflow repository pointer](workflows/README.md) and [backup and restore](docs/backup-and-restore.md).

For recovery, stop both services, export the SQLite volume, and preserve the matching `.env` separately from the data archive. Rehearse restoration in a new volume; never import a rehearsal over the working volume.

## Updating n8n

1. Read release notes and check supported ARM64 images and runner compatibility.
2. Stop services and take a verified backup with the current encryption key.
3. Change **both** image pins to the same approved release in the Compose file.
4. Run `podman compose pull` and `podman compose up -d`; check logs, readiness, and a representative workflow.
5. Review and commit the configuration change on a branch.

Database migrations can prevent simply switching back to an older image. Recover the pre-update backup with its matching version/key into a separate volume if rollback is necessary.

## Boundaries

USDZ migration, AI-provider credentials, public webhooks, PostgreSQL, Assistant services, and automatic Git synchronization are outside this initial setup. The user creates the first owner account; no sample workflow or credentials are imported automatically.

## References

- [n8n task runners](https://docs.n8n.io/deploy/host-n8n/configure-n8n/set-up-task-runners/)
- [n8n readiness monitoring](https://docs.n8n.io/deploy/host-n8n/keep-n8n-running/monitor-n8n/)
- [Workflow export/import](https://docs.n8n.io/build/manage-workflows/export-and-import/)
- [Backup and restore](https://docs.n8n.io/deploy/host-n8n/keep-n8n-running/backup-and-restore/)
