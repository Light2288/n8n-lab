# n8n-post-merge-docs — Implementation plan

> **For agentic workers:** Use `personal-workflows:spec-implement` with `superpowers:executing-plans`. Complete the checkbox steps in order.

**Slug:** `n8n-post-merge-docs`

**Status:** APPROVED

**Created:** 2026-10-25

**Updated:** 2026-10-25

**Provenance:** Approved specification, your selection of native execution, and your subsequent “PLEASE IMPLEMENT THIS PLAN” message reproducing this implementation plan, which approves the separate plan gate.

**Execution method:** Native, followed by sequential independent specification and quality reviews.

**Execution tier:** lite

**Implementation model role:** light

Native execution retains the current session model; no model switch occurs.

**Goal:** Fix outdated branch references in three README files and commit the verified changes locally.

**Architecture:** Direct Markdown edits across the two existing checkouts. No application interfaces, runtime configuration, or workflow definitions change.

**Tech Stack:** Markdown, Git, curl.

**Spec:** [Approved specification](../specs/2026-10-25-n8n-post-merge-docs.md), approved by your earlier message reproducing that specification under “PLEASE IMPLEMENT THIS PLAN”.

**Plan destination:** `docs/superpowers/plans/2026-10-25-n8n-post-merge-docs.md` in `n8n-lab`.

Both artifacts are approved. Save the approved plan before editing the operational documentation.

## Constraints and evidence

- Use the existing checkouts; preserve concurrent local changes.
- Create `docs/post-merge-docs` in each repository after plan approval.
- Preserve runtime files, secrets, workflow JSON, and historical specifications and plans.
- No pushing, merging, branch deletion, or workflow execution.
- Inspected baselines: lab `bd61550`; workflows `122848a`. Both match remote `main`. Only the new approved specification was untracked at planning time.
- Neither repository contains an architecture map or ADRs; freshness assessment is therefore not applicable.
- Verification commands follow the existing READMEs, previous repository plan, and this specification’s acceptance criteria. No test framework is needed.

## Task 1: Correct and verify documentation [size: S | risk: none | mechanical: true | clear_pattern: true | objectively_verifiable: true | concerns: none]

**Acceptance criteria:** AC-01–AC-05.

**Files:** Lab root README, lab `workflows/README.md`, workflow-repository root README, and the new specification/plan artifacts in the lab.

**Interfaces:** Consumes the existing public workflow repository’s `main` branch. Produces corrected links and clone instructions.

- [ ] **Prepare branches.** Recheck `git status --short --branch`. In each repository, run `git fetch origin`, safely fast-forward `main` with `git merge --ff-only origin/main`, then run `git switch -c docs/post-merge-docs`. Preserve the approved specification. Investigate conflicting changes without discarding them; recheck the checkout before subsequent edits.

- [ ] **Save the approved plan.** Save this plan at its destination with status APPROVED and record its approval message. Keep both artifacts uncommitted until the lab implementation branch exists.

- [ ] **Correct lab guidance.** Replace all three workflow-repository URLs in the two lab README files with `https://github.com/Light2288/n8n-workflows/tree/main`. Remove the accompanying pending-merge explanations.

- [ ] **Correct workflow clone guidance.** Change the introduction to “Clone this repository’s main branch”, use `git clone --branch main https://github.com/Light2288/n8n-workflows.git`, and delete the obsolete paragraph explaining the temporary implementation branch.

- [ ] **Verify content and scope.** Inspect the complete diff. Confirm the three operational README files contain no deleted-branch references or pending-merge wording. Confirm ownership, credential handling, explicit import/export guidance, and operating procedures remain intact. Historical artifacts and other tracked files must have no changes.

- [ ] **Verify links and clone target.** Run `curl --fail https://github.com/Light2288/n8n-workflows/tree/main > /dev/null`; expect exit 0. From the workflow checkout, run `git ls-remote origin refs/heads/main`; expect a commit followed by `refs/heads/main`.

- [ ] **Verify and commit.** Run `git diff --check` in both repositories; expect exit 0 with no output. Stage only the identified files, inspect the staged diffs, and run `git diff --cached --check`. Commit the lab changes as `docs: fix workflow repository links` and the workflow changes as `docs: clone workflows from main`.

- [ ] **Run the final review gate.** Prepare one review package covering both committed diffs, approved artifacts, changed-file lists, and observed checks. Run `personal-workflows:code-review`: specification review first, quality review only after specification PASS. Resolve findings within the approved scope and repeat affected verification.

- [ ] **Confirm completion.** Run `git status --short --branch` in both checkouts; expect clean implementation branches. Report both local commits and review results. Leave them unpushed and unmerged.

## Approval handoff

**Current stage:** 4 — implementation authorized by the separate plan approval.

Approval authorizes saving this plan, implementing, verifying, reviewing, and committing the documentation fix locally. The implementation prompt arrived after leaving Plan Mode. Pushing and merging remain outside this approval.
