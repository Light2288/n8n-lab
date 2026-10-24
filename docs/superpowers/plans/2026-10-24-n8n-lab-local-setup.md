# n8n-lab-local-setup — Implementation plan

**Slug:** `n8n-lab-local-setup`
**Status:** APPROVED
**Lifecycle:** ACTIVE
**Approval evidence:** User message beginning "PLEASE IMPLEMENT THIS PLAN:" followed by the complete revised plan in this conversation on 2026-10-24. This is separate from the earlier explicit specification approval.
**Provenance:** Revised plan presented in chat after specification approval, then explicitly requested for implementation. Supersedes the premature inactive disk draft. Task headings normalized for execution tooling; scope unchanged.
**Current stage:** Stage 4 — implementation
**Execution tier:** full
**Implementation model role:** full
**Execution method:** Native execution, followed by independent specification and quality reviews. Inline execution does not switch the parent model.
**Goal:** Create a persistent local n8n environment and publish its reviewed configuration on an unmerged GitHub branch.
**Architecture:** Podman Compose runs n8n and its external task runner. SQLite and instance settings persist in a named volume. Only the editor is published to loopback. Git stores configuration, documentation, and manually reviewed workflow exports.
**Tech Stack:** Existing Podman 6.1.2 and podman-compose 1.6.0; official ARM64 n8n and runner images pinned to `2.44.6`; SQLite; Git and GitHub CLI.
**Spec:** `docs/superpowers/specs/2026-10-24-n8n-lab-local-setup.md`, approved by the user message: "I approve the specification `n8n-lab-local-setup`".

## Global constraints

Work in the existing checkout at `/Users/davide/Personal/Projects/n8n-lab`; no worktree. Preserve existing state. Do not install Docker, use privileged containers, mount the container socket, or mount host project directories. Keep secrets out of tracked files, tool output, review packages, commits, and GitHub. Keep the current VM resources initially. Owner-account creation remains the user's responsibility. Push is authorized; merge and force-push are excluded.

## Task 1: Configure and start the lab

**Task routing:** [size: M | risk: security | mechanical: false | clear_pattern: true | objectively_verifiable: true | concerns: security,auth]
**Acceptance criteria:** AC-02–AC-05.

- [x] Recheck the project directory, Podman VM, availability of port 5678, and GitHub repository name. Preserve any newly existing state; investigate conflicts before proceeding.
- [x] After both approvals and leaving Plan Mode, save the approved artifacts. Initialize Git with an empty baseline commit on `main`, then create `chore/local-n8n-setup` in the existing checkout. Commit the artifacts only on that branch. No worktree is needed.
- [x] Add Compose configuration, empty environment placeholders, Git exclusions, and initial setup instructions. Exclude actual environment files, databases, credential exports, execution data, private samples, and backups.
- [x] Configure `n8n` using `docker.io/n8nio/n8n:2.44.6` and `task-runners` using `docker.io/n8nio/runners:2.44.6`.
- [x] Set the Compose project name to `n8n-lab`. Use the named volume `n8n-lab-data`, mounted only in n8n at `/home/node/.n8n`.
- [x] Publish only `127.0.0.1:5678:5678`. Keep broker port 5679 and runner health port 5680 unpublished. Retain the images' non-root users; add no privileged mode, container socket, or host-directory mounts.
- [x] Configure n8n explicitly: external task runners, broker listening on the container network, runner broker URI `http://n8n:5679`, `TZ` and `GENERIC_TIMEZONE` set to `Europe/Rome` in both services, local HTTP URLs, `N8N_SECURE_COOKIE=false`, settings-file permission enforcement, and blocked workflow environment access. Use a Node HTTP readiness check against `/healthz/readiness`; start the runner after n8n becomes healthy.
- [x] Require nonempty `N8N_ENCRYPTION_KEY` and `N8N_RUNNERS_AUTH_TOKEN` through Compose `${VARIABLE:?message}` interpolation. Map variables explicitly: n8n receives both secrets; the runner receives only its authentication token.
- [x] Generate two independent random secrets with Python's standard `secrets` module. Create the ignored `.env` with exclusive creation and mode `0600`; never overwrite an existing file or print its values.
- [x] Verify configuration rejection for each missing or empty secret using dummy values and `--env-file /dev/null`. Verify that a valid dummy pair passes. The installed provider has already demonstrated this behavior, so no preflight wrapper is planned.
- [x] Validate the real configuration with rendered output suppressed, then run `podman compose up -d`.
- [x] Confirm both services run, the runner registers, the editor loads, and `curl --fail http://localhost:5678/healthz/readiness` returns HTTP 200. Leave owner-account creation to the user.
- [x] Inspect ports, mounts, users, privilege flags, and environment-variable names without displaying secret values. Check `.env` permissions and Git exclusion.
- [x] Measure resource use with `podman stats --no-stream`. Retain the current 2 GiB memory allocation initially.
- [x] Commit the reviewed configuration and approved artifacts on the implementation branch.

## Task 2: Verify persistence, recovery, and workflow conventions

**Task routing:** [size: M | risk: data | mechanical: false | clear_pattern: true | objectively_verifiable: true | concerns: data]
**Acceptance criteria:** AC-06–AC-08.

- [ ] Stop both services before inspecting persistent data. Record private evidence of the database, migration records, and instance-settings checksum.
- [ ] Run `podman compose down` without volume deletion, followed by `podman compose up -d`. Confirm the same named volume remains attached, database state and settings persist, and readiness returns HTTP 200. Do not require the SQLite file's raw checksum to remain unchanged after startup.
- [ ] Stop both services again for a consistent backup. Create a timestamped directory under `~/.local/share/n8n-lab/backups`, with directory permissions `0700`.
- [ ] Export using `podman volume export n8n-lab-data --output <private-archive>`. Keep archives at `0600`. Preserve the corresponding `.env` separately from the data archive, also at `0600`.
- [ ] Create a uniquely named temporary volume and restore with `podman volume import <temporary-volume> <private-archive>`. Export it again and compare normalized per-file contents and checksums, including SQLite and settings. Ignore tar ordering; never print file contents.
- [ ] Confirm the restored contents match. Delete only the temporary restore volume and comparison archive. Retain the backup, restart the working stack, and verify readiness.
- [ ] Complete documentation covering prerequisites, first-owner setup, start/stop, logs, readiness, stopping versus deleting volumes, Mac sleep and stopped-VM interruptions, local HTTP/public-exposure boundary, backup/recovery, secure encryption-key retention, and backups before updating both image pins together.
- [ ] Document manual workflow versioning: export JSON through the editor, inspect pinned data, inline authentication, headers, private samples, and credential references, then commit reviewed exports.
- [ ] Explain explicit JSON import and separate credential recreation. Editor saves and Git pulls do not synchronize workflows automatically.
- [ ] Commit the verified operating and workflow documentation.

## Task 3: Review and publish the unmerged branch

**Task routing:** [size: S | risk: security | mechanical: false | clear_pattern: true | objectively_verifiable: true | concerns: security]
**Acceptance criteria:** AC-01 and final confirmation of AC-02–AC-08.

- [ ] Run final readiness, service-status, secret-validation, isolation, permissions, and Git-exclusion checks. Run `git diff --check` and inspect the complete tracked-file list.
- [ ] Prepare a redacted review package containing the approved specification, approved plan, branch diff, and verification results.
- [ ] Run the sequential `personal-workflows:code-review` gate: specification first, then quality. Resolve findings before publication. Material scope/plan changes return to their respective approval gate.
- [ ] Inspect the complete committed tree and history for actual secret values and private content without displaying matching values.
- [ ] Create the public remote using `gh repo create Light2288/n8n-lab --public --source=. --remote=origin`.
- [ ] Push the empty baseline `main` and reviewed `chore/local-n8n-setup` branch. Do not merge or force-push.
- [ ] Verify public visibility, matching local/remote implementation-branch commit IDs, and a clean working tree.
- [ ] Leave services running and open the editor for the user's owner-account setup. Provide the local URL, public implementation-branch link, documentation link, and verification results.

## Verification and defaults

Checks exercise the actual Compose provider, running containers, persistence, and restored backup. No new unit-test framework is needed for this configuration-only project.

| Criterion | Decisive evidence |
|---|---|
| AC-01 | Public remote and matching implementation-branch commit |
| AC-02 | Both services running through Podman without privileged mode |
| AC-03 | Editor loads; readiness HTTP 200 |
| AC-04 | Loopback-only publication; runner has no n8n volume or encryption key |
| AC-05 | Missing/empty secrets rejected; real secrets ignored/untracked |
| AC-06 | Database and settings survive container recreation |
| AC-07 | Isolated restore matches stopped-volume backup |
| AC-08 | Reviewed onboarding, workflow, recovery, operating instructions |

New repository: no prior HEAD, architecture map, ADRs, or test framework. Interfaces are the local editor, readiness endpoint, required environment variables, and manual workflow JSON exchange. USDZ migration, AI-provider credentials, PostgreSQL, public webhooks, Assistant services, and automatic Git synchronization remain excluded.

## Approval record

Specification and this implementation plan received separate explicit approvals in this conversation. The plan approval authorizes saving both artifacts, implementation, verification, independent final review, commits, and publication of the unmerged branch. No merge is authorized.
