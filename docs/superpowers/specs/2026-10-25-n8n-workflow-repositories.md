# n8n-workflow-repositories — Specification

**Slug:** `n8n-workflow-repositories`
**Type:** chore
**Status:** APPROVED
**Provenance:** Approved repository-separation direction, public-repository choice, and the user's message “i confirm the scope and design”; existing n8n-lab documentation and `/Users/davide/Personal/Projects/opencode_notes/career-tech-analysis` context. Presented as a DRAFT specification in chat and approved by the subsequent “PLEASE IMPLEMENT THIS PLAN” message reproducing that specification. Under AGENTS.md, that message approves the pending specification gate only.

## Problem and current behavior

`n8n-lab` currently combines runtime documentation with instructions to store workflow exports in its own `workflows/` directory. Its setup instructions also reference the implementation branch, although that branch has already been merged into `main`.

The user has created the owner account and completed the smoke test. Its exported JSON has not yet been inspected.

The goal is to keep the runtime reusable while giving workflow experiments a clear public home.

## Selected design

Use two repositories:

- **`n8n-lab`** owns Podman configuration, local setup, updates, backups, and recovery.
- **`n8n-workflows`** owns reviewed workflow exports and their usage documentation.

Create the new public repository as `Light2288/n8n-workflows`, with its local checkout at `/Users/davide/Personal/Projects/n8n-workflows`.

Start with one shared workflow repository. A separate repository per small workflow would add unnecessary maintenance. A workflow belongs in a product repository only when it becomes an officially maintained product integration; the USDZ integration remains deferred.

### Workflow organization and first example

Group exports by purpose under `workflows/`. Each example has a workflow JSON file and a short README covering purpose, prerequisites, import instructions, execution, and expected output.

The first example is the user's existing **Manual Trigger → JavaScript Code** smoke test. Inspect its actual export before publication. It should demonstrate deterministic local execution without credentials, external services, or private data.

The repository's root README explains the relationship with `n8n-lab` and provides an index of examples.

### Documentation and workflow lifecycle

Update the lab's clone instructions to use `main`. Replace its workflow-storage guidance with a link to the new repository, retaining a short pointer rather than duplicate authoritative JSON.

Document this manual lifecycle:

1. Edit and execute a workflow in n8n.
2. Export its JSON through the editor.
3. Inspect and sanitize the export.
4. Commit the reviewed definition in `n8n-workflows`.
5. Import JSON explicitly when applying a Git version to an instance.

Explain that editor saves and Git pulls do not synchronize automatically. Credentials remain in the local instance and must be configured separately when needed.

## Acceptance criteria

| ID | Required outcome |
|---|---|
| **AC-01** | The new local repository exists with a public `Light2288/n8n-workflows` remote. Published implementation-branch commits match their local counterparts. |
| **AC-02** | Repository documentation clearly assigns runtime operations to `n8n-lab` and workflow examples to `n8n-workflows`, with working links between them. |
| **AC-03** | The actual smoke-test export is valid JSON, contains the connected Manual Trigger and JavaScript Code nodes, and has documented expected output. An imported copy executes successfully on the existing instance. |
| **AC-04** | Published files and committed history contain no actual secrets, private pinned data, sensitive samples, or instance-specific identity information unnecessary for import. Ignore rules exclude local secrets and private artifacts. |
| **AC-05** | The lab's setup documentation clones `main` and directs workflow exports to the new repository without duplicating their authoritative JSON. |
| **AC-06** | Documentation covers export review, explicit import, credential handling, and the absence of automatic Git synchronization. |
| **AC-07** | Existing runtime configuration, owner account, credentials, and saved workflows are preserved. Verification imports use a separate workflow rather than overwriting the original. |
| **AC-08** | Changes are reviewed and published on implementation branches. Merging remains a separate action. |

Review must assess publication readiness before pushing; final acceptance additionally verifies the public remotes and published commits.

## Constraints and edge cases

- Use the existing Podman-based n8n instance; install no Docker components.
- If the export contains sensitive material, sanitize it and verify the resulting example before publication.
- If the existing workflow cannot be obtained or differs materially from the agreed smoke test, report the discrepancy rather than claiming it was verified.
- Keep workflow JSON compatible with the installed n8n `2.44.6`; document that as the initially verified version.
- Complete the remaining approval gates before creating repositories, changing product files, or publishing branches.

## Out of scope

USDZ automation, additional n8n instances, container reconfiguration, database changes, credential migration, public webhooks, automatic Git synchronization, and repository merges.

## Notes

Scope/design confirmation preceded the DRAFT specification. Specification approval authorizes implementation planning only; implementation requires separate approval of that plan. The specification and plan use the identical filename `2026-10-25-n8n-workflow-repositories.md` in their respective Superpowers directories. Both remain uncommitted until the approved implementation branches are created.
