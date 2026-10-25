# n8n-post-merge-docs — Specification

**Slug:** `n8n-post-merge-docs`

**Type:** chore

**Status:** APPROVED

**Created:** 2026-10-25

**Updated:** 2026-10-25

**Provenance:** Documentation findings, the request “fix these findings”, scope confirmation “scope and design confirmed”, and specification approval through the subsequent “PLEASE IMPLEMENT THIS PLAN” message reproducing this specification. That message advances the pending specification gate only; the implementation plan requires separate approval.

## Problem and desired outcome

Both repository changes have been merged into `main`, and their remote implementation branches have been deleted. The documentation still links to the deleted workflow branch and instructs readers to clone it.

Update the operational documentation to use the published `main` branch.

## Required changes

- In `n8n-lab/README.md` and `n8n-lab/workflows/README.md`, replace workflow-repository links with `https://github.com/Light2288/n8n-workflows/tree/main`.
- In `n8n-workflows/README.md`, use:
  ```sh
  git clone --branch main https://github.com/Light2288/n8n-workflows.git
  ```
- Remove wording describing these changes as unmerged or awaiting a future merge.
- Preserve the distinction between runtime configuration and workflow definitions.

Direct Markdown edits are sufficient; no tooling or runtime changes are needed.

## Acceptance criteria

| ID | Required outcome |
|---|---|
| AC-01 | The three operational README files contain no references to the deleted implementation branch or pending initial merge. |
| AC-02 | Updated GitHub links resolve successfully, and the documented clone target exists remotely. |
| AC-03 | Repository ownership, workflow import/export instructions, and operating procedures remain accurate. |
| AC-04 | Runtime configuration, secrets, workflow JSON, and historical specifications and plans remain unchanged. |
| AC-05 | Changes are reviewed, verified with `git diff --check`, and committed on a new documentation branch in each existing checkout. |

## Constraints and boundaries

Preserve existing local changes if either checkout changes before implementation. Create branches only after separate specification and plan approval.

Pushing, merging, branch cleanup, and running or modifying workflows are outside this fix.

## Approval and artifact lifecycle

The specification is approved. Approval authorizes drafting the separate implementation plan only. Implementation requires subsequent plan approval.

The plan will use the same filename in `docs/superpowers/plans/`. Keep both artifacts uncommitted until both approvals have been received and the implementation branch has been created.
