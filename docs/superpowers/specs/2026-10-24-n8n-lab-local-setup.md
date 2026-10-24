# n8n-lab-local-setup — Specification

**Slug:** `n8n-lab-local-setup`
**Type:** chore
**Status:** APPROVED
**Approval evidence:** User message on 2026-10-24: "I approve the specification `n8n-lab-local-setup`". This approval authorizes implementation planning only.
**Provenance:** Specification discussed with the user on 2026-10-24. Public GitHub repository, external task runner, and deferred USDZ migration were confirmed in conversation. Approval status was reset to DRAFT following the user's clarification that specification and plan require separate, sequential approvals. Renamed from `docs/superpowers/specs/2026-10-24-n8n-lab-local-setup-design.md` to share the plan's filename, as requested by the user.

## Goal

Create a reproducible local n8n environment for personal experiments, with configuration and reviewed workflow exports versioned in a public repository.

Local directory: `/Users/davide/Personal/Projects/n8n-lab`.
GitHub repository: `Light2288/n8n-lab`.

## Agreed setup

- Use the existing Podman installation and Compose provider.
- Run n8n and its external task runner, both pinned to `2.44.6`. Both official images have verified ARM64 support.
- Use SQLite and a named volume mounted at `/home/node/.n8n`.
- Publish the editor at `http://localhost:5678`, bound only to `127.0.0.1`. Keep the runner broker internal to the container network.
- Use `Europe/Rome` for application and system timezones.
- Keep a persistent encryption key and runner authentication token in an ignored `.env` file with restricted permissions. Missing secrets prevent startup.
- Support local HTTP login explicitly; document that this configuration requires changes before public exposure.
- Leave owner-account creation to the user in the browser.

The external runner follows [n8n's isolation guidance](https://docs.n8n.io/deploy/host-n8n/configure-n8n/set-up-task-runners.md). The Assistant sandbox stack is excluded.

## Repository and operating conventions

Track Compose configuration, placeholder environment settings, setup instructions, a workflow directory, and backup/restore instructions.

Exclude actual secrets, databases, credential exports, execution data, private samples, and backups from Git. Document startup, shutdown, logs, readiness, explicit workflow JSON export/import, review of pinned data and embedded secrets, consistent SQLite backups, isolated restore rehearsals, and backups before image updates.

Create the public remote and push the reviewed implementation branch after specification and implementation-plan approval. Follow the feature-branch workflow; merging remains separate.

## Acceptance criteria

- **AC-01:** The standalone Git repository has the public GitHub remote and contains reviewed configuration and documentation.
- **AC-02:** `podman compose up -d` starts both services without Docker components or privileged containers.
- **AC-03:** The editor loads locally and `/healthz/readiness` returns HTTP 200.
- **AC-04:** Only the editor port is published, bound to loopback. The runner has no n8n data-volume mount or encryption-key setting.
- **AC-05:** Missing secrets cause configuration validation to fail; actual secret files remain ignored and untracked.
- **AC-06:** Recreating containers preserves the database and instance configuration through the named volume.
- **AC-07:** A backup restored into a temporary volume preserves backed-up contents without overwriting the working instance.
- **AC-08:** The README explains first login, daily commands, workflow versioning, recovery, and the limitations of Mac sleep or a stopped Podman VM.

## Boundaries and approval

Exclude USDZ migration, AI-provider credentials, public webhooks, PostgreSQL, Assistant services, and automated Git synchronization. Keep the current VM allocation initially and inspect resource use during implementation.

Explicit specification approval is recorded above. The revised implementation plan was subsequently presented in chat and separately approved by the user message beginning "PLEASE IMPLEMENT THIS PLAN:" and reproducing that plan on 2026-10-24. The workflow is at Stage 4. Both artifacts are saved before configuration changes and will be committed only on the implementation branch. Merging remains excluded.
