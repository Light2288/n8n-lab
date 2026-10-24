# n8n-workflow-repositories Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking. The personal adapter and the user's Git/approval rules take precedence; this plan proposes native execution through personal-workflows:spec-implement and superpowers:executing-plans, followed by the independent sequential final review.

**Slug:** `n8n-workflow-repositories`
**Status:** APPROVED
**Provenance:** `docs/superpowers/specs/2026-10-25-n8n-workflow-repositories.md`; the user's scope/design confirmation and subsequent “PLEASE IMPLEMENT THIS PLAN” message reproducing the DRAFT specification. That message approves the specification, not this previously unpresented plan.
**Execution tier:** full
**Implementation model role:** full
**Execution method:** Native execution, approved by the user's message “ok / plan approved”; one independent sequential specification/quality review gate after publication.
**Goal:** Separate portable workflow examples from the running lab and publish both repositories' reviewed changes on unmerged branches.
**Architecture:** The existing Podman instance remains the runtime. n8n-lab owns operations; n8n-workflows owns manually exported, sanitized, explicitly imported definitions. Git does not synchronize the running database.
**Tech Stack:** Git, GitHub CLI, Python 3 standard library for one-off validation, the existing n8n 2.44.6 editor and Podman runtime. No new runtime dependencies or test framework.
**Spec:** `/Users/davide/Personal/Projects/n8n-lab/docs/superpowers/specs/2026-10-25-n8n-workflow-repositories.md` — APPROVED; this plan received its separate approval in the subsequent user message “ok / plan approved”.

## Global constraints

- Existing checkout `/Users/davide/Personal/Projects/n8n-lab`; new checkout `/Users/davide/Personal/Projects/n8n-workflows`. No worktrees.
- Public new remote `Light2288/n8n-workflows`; implementation branch `chore/workflow-repositories` in both repositories. No merges or force-pushes.
- Preserve the existing owner account, credentials, original workflows, volume, image pins, secrets, and runtime configuration. No Docker installation or container restart/reconfiguration is required.
- Use the actual smoke-test export; do not fabricate a replacement. Stop and report material differences from Manual Trigger → JavaScript Code or an inaccessible source.
- Keep private exports and review evidence outside both repositories with restrictive permissions. Never print secrets, authenticated session tokens, or private export contents.
- USDZ automation, new services, public webhooks, credential migration, database changes, automatic Git synchronization, and merges remain excluded.
- Specification and plan retain the same filename in their respective directories. Do not commit artifacts, update main, create branches, or implement until this plan has received separate approval.

## Review focus

1. Missing or materially different source workflow: obtain the real export and check its graph before producing the public example (Task 1).
2. Hidden data or identity in an otherwise simple export: inspect all fields, node code, pinned data, metadata, and the full Git history (Tasks 1 and 3).
3. Import overwriting the original: remove instance workflow identity, import into a new workflow, and compare the original before/after (Task 1).
4. Links into an empty default branch: link the lab directly to the published workflow implementation branch until a separate merge (Tasks 2 and 3).
5. Local work or a newly existing repository appearing during the approval gap: recheck paths, remotes, and branches; preserve state and investigate conflicts (Tasks 1 and 3).

## Grounding and verification sources

- Lab HEAD inspected at `8d9d4a70c8b34d9be93ebd0c1daa826197f3ef42`, clean on main tracking origin/main. Recheck before execution.
- No architecture map or ADRs exist in the lab's tracked file list. `docs/architecture/` and `docs/adr/` are absent, so map freshness and ADR status are not applicable. Do not create a map or ADR for this bounded change.
- README.md and workflows/README.md provide the current Podman status/readiness commands, Download/Import from File procedure, Git export-review commands, and credential/pinned-data warnings. Their current guidance places JSON in the lab and clones chore/local-n8n-setup; Task 2 replaces that guidance.
- compose.yaml pins both services to 2.44.6 and defines the unchanged volume and local endpoint. .gitignore provides the existing secret/runtime/private-file exclusion patterns.
- GitHub CLI `gh repo create --help` confirms public creation from an existing local repository without automatic push; `gh repo view --json` supplies remote visibility evidence. Standard Git inspection/diff commands follow the existing setup plan and current Git interface. Python JSON validation uses the installed standard library, not an invented project test command.
- Planning observations: both services running, editor readiness HTTP 200; new local directory absent; GitHub lookup reports the new repository absent. No accessible browser tab or actual workflow export was available during planning; those are not claimed as verified.

## File responsibilities

- n8n-workflows: README.md indexes examples and owns the shared export/import/review procedure; .gitignore excludes local/private artifacts; workflows/local-smoke-test/workflow.json is the portable definition; workflows/local-smoke-test/README.md documents this example's prerequisites, steps, and actual expected output.
- n8n-lab: README.md owns runtime onboarding and links to the workflow repository; workflows/README.md becomes a short pointer. Runtime files and backup instructions remain unchanged. The governing specification and this plan are committed with the lab documentation changes.

### Task 1: Create and verify the portable smoke-test repository [size: M | risk: security | mechanical: false | clear_pattern: true | objectively_verifiable: true | concerns: security,data]

**Acceptance criteria:** AC-02, AC-03, AC-04, AC-06, AC-07.
**Risk:** A real workflow export may contain sensitive data or instance identity, and import must not overwrite the source.
**Files:** Create n8n-workflows/README.md, .gitignore, workflows/local-smoke-test/workflow.json, and workflows/local-smoke-test/README.md. Read but do not edit the lab runtime files.
**Interfaces:** Consumes the existing saved smoke test and running editor; produces a sanitized JSON definition, verified imported copy, documented expected result, and a committed local implementation branch for Task 3.

- [ ] Recheck `git status --short --branch`, `git remote -v`, local directory existence, and `gh repo view Light2288/n8n-workflows --json nameWithOwner,visibility,url`. Expected: only the approved artifact files may be untracked in the lab, correct existing remote, and no conflicting new repo. Preserve or investigate any different state before continuing.
- [ ] Record approval messages and read both saved approved artifacts. After plan approval, update its status to APPROVED. On lab main, run `git fetch origin` and `git merge --ff-only origin/main`, preserving the uncommitted artifacts, then `git switch -c chore/workflow-repositories`. Initialize the new local repository with `git init -b main`, an empty `chore: initialize repository` baseline commit, then create the same implementation branch. Expected: no implementation is placed on main; no worktree or existing state is discarded.
- [ ] Capture the runtime baseline with `podman compose ps` and the README's readiness request. Record the named-volume attachment and fingerprints of compose.yaml and .env without exposing their values. In the existing authenticated editor, locate and download the user's smoke test to private scratch space, not either repo. If sign-in is needed, let the user sign in; do not create a replacement owner or retrieve passwords.
- [ ] Inspect the complete actual export privately. Confirm exactly the intended connected Manual Trigger and JavaScript Code graph, no external service/credential dependency, and deterministic harmless code. Record the source workflow identity and a private canonical fingerprint for preservation checks. A missing or materially different source is a stop/report condition, not permission to substitute a new workflow.
- [ ] Produce workflows/local-smoke-test/workflow.json from the inspected export. Retain the functional node definitions, connection graph, settings, and actual code; set a portable name `Local smoke test`. Remove instance workflow ID, version/instance/ownership metadata, timestamps, tags, pinned data, and other nonfunctional private state. Keep internal node identifiers needed by the definition. Do not carry credentials or static execution state; the agreed example requires neither. Keep the example inactive if an active field is present.
- [ ] Import from File into a new workflow, confirm its identity differs from the source, and save it as `Local smoke test (import verification)`. Execute manually and compare the observed output with the source code's deterministic expected result. Document the observed value rather than assuming the earlier suggested sample was copied exactly. Leave the verified copy inactive and preserve the original.
- [ ] Re-export the original to private scratch and compare its canonical definition to the baseline. Confirm the owner session still works, no credential operation occurred, runtime fingerprints/volume attachment are unchanged, and readiness remains HTTP 200. Expected: only the additional verification workflow/execution is new; no original definition is modified.
- [ ] Write the four repository files. The root README links runtime setup on the lab's existing main, indexes the example, and documents manual export → inspection → commit → explicit import. The example README records n8n 2.44.6, no credentials/external services, execution steps, and actual output. Explain JSON definitions versus full instance backups and credential recreation.
- [ ] Adapt the lab's existing ignore patterns for .env files, key files, local instance/database data, credential/execution exports, private/scratch inputs, backups, and macOS artifacts. Keep the intended workflow JSON trackable. Do not add a license, CI framework, automated sanitizer, or synchronization service.
- [ ] Run `python3 -m json.tool workflows/local-smoke-test/workflow.json` with output suppressed. Use one-off Python inspection to assert the expected two node types, connected edge, preserved functional code, and absence of credentials, pinned data, instance IDs, and unsafe/private fields. Expected: valid JSON and all assertions pass. Manually inspect complete contents; syntax or field-name checks alone are not a secret review.
- [ ] Run `git check-ignore --no-index --stdin` with representative private paths and `git check-ignore --no-index workflows/local-smoke-test/workflow.json` separately. Expected: private paths match exclusions; the public example does not match. Check all relative documentation links against real files.
- [ ] Stage only the four explicit new files, inspect `git diff --cached`, run `git diff --cached --check`, and commit `feat: add portable local n8n smoke test`. Expected: complete sanitized file contents are visible in the staged diff and only intended files enter history.

### Task 2: Point lab documentation to its workflow repository [size: S | risk: none | mechanical: true | clear_pattern: true | objectively_verifiable: true | concerns: none]

**Acceptance criteria:** AC-02, AC-05, AC-06, AC-07.
**Files:** Modify lab README.md and workflows/README.md; commit the already approved specification and plan in docs/superpowers/.
**Interfaces:** Consumes Task 1's repository layout; produces accurate runtime-only onboarding and a pointer to the public workflow implementation branch.

- [ ] Change the lab clone command to `git clone --branch main https://github.com/Light2288/n8n-lab.git`; remove the obsolete until-merged explanation. Keep key generation, first login, operations, updates, and recovery instructions intact.
- [ ] Update the introductory/workflow wording so the lab versions runtime configuration and documentation. Link examples to `https://github.com/Light2288/n8n-workflows/tree/chore/workflow-repositories` while that work is unmerged. Explain the temporary branch target so it is not mistaken for automatic synchronization or a required runtime checkout.
- [ ] Replace workflows/README.md with a short link to that same authoritative repository and the distinction between exported JSON and the running database. Do not keep duplicate workflow JSON or duplicate the full export procedure in the lab.
- [ ] Inspect `git diff` and use one-off link checks for relative paths. Expected: only these documentation edits and the two approved artifacts; no changed compose.yaml, .env.example, .gitignore, or backup/recovery procedure. `git ls-files 'workflows/*.json' 'workflows/**/*.json'` returns no lab exports.
- [ ] Run `git diff --check`, stage only the two documentation files and the two artifacts, inspect `git diff --cached`, then commit `docs: separate runtime setup from workflow examples`. Expected: clean whitespace, correct main clone guidance, single authoritative workflow location, and unchanged runtime configuration.

### Task 3: Review publication readiness, publish, and run the final gate [size: M | risk: security | mechanical: false | clear_pattern: true | objectively_verifiable: true | concerns: security]

**Acceptance criteria:** AC-01 through AC-08.
**Risk:** Publishing a public repository exposes its complete reachable history; scan and review everything before any push.
**Files:** No additional product files planned. Private scratch review package and observed verification evidence only; any necessary remedies remain within approved files/scope.
**Interfaces:** Consumes both committed implementation branches and verification evidence; produces public matching branch heads, one independent final review result, and a handoff with no merges.

- [ ] Perform the prepublication review personally: read both complete branch diffs and tracked file lists, inspect every new JSON/code/documentation file, check intended public links, and review the complete reachable history for each branch. Scan Git blob contents against the actual lab secret values in memory and inspect for embedded authentication, private samples, raw exports, owner/workflow/instance metadata, and other sensitive content. Report only pass/fail and safe locations, never matching values. Expected: no actual secrets/private content anywhere to be pushed. A match prevents publication and must be resolved without pushing that history.
- [ ] Re-run JSON/ignore/link checks, `git diff --check`, `git status --short --branch`, and Podman readiness. Confirm the original workflow/runtime preservation evidence, imported-copy result, clean trees, and branch commit IDs. Expected: all local acceptance evidence available and publication-ready contents. Keep evidence outside the repositories so reviewed commit IDs remain stable.
- [ ] Recheck that the GitHub name is still available. From the new repository, run `gh repo create Light2288/n8n-workflows --public --source=. --remote=origin` without --push, --add-readme, or a template. Push its empty main baseline with `git push -u origin main`, then its reviewed branch with `git push -u origin chore/workflow-repositories`. Push the lab branch with `git push -u origin chore/workflow-repositories`. Do not merge, force-push, or rewrite the lab's main.
- [ ] Verify visibility with `gh repo view Light2288/n8n-workflows --json nameWithOwner,visibility,url,defaultBranchRef`. In both repositories compare `git rev-parse HEAD` with `git ls-remote origin refs/heads/chore/workflow-repositories`; verify main remains the expected existing/baseline commit. Resolve the published cross-repository branch links and the example's relative links. Expected: PUBLIC, correct remotes/default main, identical implementation commit IDs, working links, and unmerged branches.
- [ ] Run the integrated `personal-workflows:code-review` gate once over the complete two-repository change. Use one redacted self-contained scratch package with approved spec/plan, exact base/head IDs, both complete diffs and changed-file lists, required file contents, all AC evidence including actual public remotes, review mode, and the enclosing native execution fix-loop policy. Create GATE_RUN_ID and REVIEWED_STATE_ID, finalize the package, and pass its SHA-256 out-of-band. Dispatch review-spec first and review-quality only after a fresh matching SPEC REVIEW: PASS. Do not substitute reviewers or carry forward the previous milestone's exception.
- [ ] Apply the enclosing workflow's permitted remedies, verify affected behavior, commit and push corrections to the same branches, and refresh the review package/identities for a permitted review follow-up. Stop on a blocked gate or exhausted fix loop; do not invent PASS or claim completion. Material scope/plan changes return to their approval gates. Because final review follows publication, findings can require follow-up public commits; the earlier full secret/content review remains mandatory.
- [ ] After the gate passes, recheck published/local branch equality, clean working trees, editor readiness, and working cross-repository links. Do not modify reviewed tracked files merely to append execution notes; retain final evidence in scratch and the user-facing handoff. Provide both branch URLs, workflow JSON/docs links, the imported workflow name and verified result, and explicit merge status. Leave the existing services running.

## Acceptance and review ordering

The specification calls for reviewing publication readiness before pushing and verifying remote results afterward. This plan makes those checkpoints explicit: the implementer performs the complete prepublication content/security review; the formal independent whole-change specification → quality gate runs after publication so AC-01 and AC-08 have observed evidence. This is a plan decision for approval, not a waiver or a claim of independent prepublication review. No merge happens at either checkpoint.

| Criterion | Task and decisive evidence |
|---|---|
| AC-01 | Task 3: public remote and identical local/published branch heads |
| AC-02 | Tasks 1–3: ownership guidance, real layout, and resolved published links |
| AC-03 | Task 1: inspected real export, valid portable JSON, successful imported-copy execution |
| AC-04 | Tasks 1 and 3: full-file/history review, private-path exclusions, secret-value scan |
| AC-05 | Task 2: main clone command, pointer only, no lab workflow JSON |
| AC-06 | Tasks 1 and 2: explicit export/review/import and credential/sync limitations |
| AC-07 | Tasks 1–3: source definition/fingerprints preserved, separate inactive import, unchanged runtime and owner access |
| AC-08 | Task 3: prepublication review, published unmerged branches, final sequential independent review |

## Approval handoff

**Current stage:** Stage 4 — implementation authorized.
**Approved artifact:** This plan for n8n-workflow-repositories, approved by the user with “ok / plan approved”.
**Approval authorizes:** Stage 4 native implementation, scoped import verification, commits, public remote creation and branch pushes, followed by the formal independent review in the order above. No merge, force-push, or expansion to USDZ automation is authorized. Until approval, only the specification and plan artifacts may be saved and both stay uncommitted.
