---
title: "Feature Specification: Devin lineage write scope"
description: "Record the verified runtime fix that OS-confines cli-devin fan-out writes to the bound lineage directory."
trigger_phrases:
  - "Devin lineage write scope"
  - "cli-devin containment violation"
  - "fanout Devin sandbox cwd"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "specs/system-deep-loop/039-devin-lineage-write-scope"
    last_updated_at: "2026-08-17T00:12:16.000Z"
    last_updated_by: "sol"
    recent_action: "Recorded and verified the Devin lineage write-scope fix."
    next_safe_action: "Decide whether to merge the isolated fanout-run.cjs fix into the primary runtime."
    blockers: []
    key_files:
      - "specs/system-deep-loop/039-devin-lineage-write-scope/spec.md"
      - ".opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "system-deep-loop-039-devin-lineage-write-scope"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: Devin lineage write scope

<!-- SPECKIT_LEVEL: 1 -->
<!--
SELF-CHECK:
- Confirm the artifact states the current problem, intended outcome, scope, and verification evidence.
- Remove placeholders, stale status, and claims that are not backed by a check.
FAILURE MODES:
- Scope drift, vague acceptance criteria, and optimistic done-language without evidence.
-->

---

<!-- ANCHOR:metadata -->
## 1. METADATA

| Field | Value |
|-------|-------|
| **Level** | 1 |
| **Priority** | P0 |
| **Status** | Complete |
| **Created** | 2026-08-17 |
| **Branch** | `worktrees/012-sk-vision` |
| **Parent Spec** | N/A - standard packet |
| **Predecessor** | N/A |
| **Successor** | N/A |
| **Handoff Criteria** | The isolated fix and verification are recorded; merging `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` into the shared primary runtime remains an operator decision. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

This is a **standard Level-1 packet** recording an already applied and verified runtime fix.

**Scope Boundary**: Document the two edits in the worktree's `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`, their verified effect, and the separate `salvage_miss` follow-up. Do not modify the shared primary runtime or generate packet metadata.

**Dependencies**:
- The worktree-local `fanout-run.cjs` contains both verified edits.
- The reported GLM-5.2-max / cli-devin research iteration completed without a containment violation.

**Deliverables**:
- Five Markdown packet documents matching the approved exemplar structure.
- A closeout record of the verified runtime behavior and remaining follow-up.

**Changelog**:
- Record the fix and verification in this packet's `implementation-summary.md`.
<!-- /ANCHOR:phase-context -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement
Deep-loop fan-out lineages using the cli-devin executor, including GLM 5.2 max, failed terminally when a leaf wrote outside its bound lineage directory. The observed leaf edited `.opencode/skills/system-deep-loop/runtime/lib/deep-loop/executor-config.ts` and `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`; the containment guard reverted those writes and killed the lineage. cli-opencode and native executors did not reproduce the violation because they respected the prompt's lineage-directory boundary.

The root cause was that `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` spawned every executor with `cwd: process.cwd()`. Devin's `--sandbox` workspace-write boundary therefore covered the whole repository. The lineage boundary was only prompt-enforced and detected post-hoc, so a model that ignored the prompt could write anywhere in the repository.

### Purpose
Record the verified fix that aligns Devin's OS sandbox write boundary with the bound lineage directory while preserving access to the leaf contract file.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope
- Scope cli-devin lineage subprocesses to `cwd: lineageDir` while leaving other executors at `process.cwd()`.
- Resolve the deep-loop skill contract path from the repository root before dispatch.
- Record the successful GLM-5.2-max / cli-devin verification and clean runtime-file result.
- Record `salvage_miss` as a separate known follow-up.

### Out of Scope
- Merging the worktree-local change into the shared primary runtime.
- Changing cli-opencode or native executor cwd behavior.
- Fixing the separate `salvage_miss` persistence issue.
- Generating `description.json` or `graph-metadata.json`.
- Committing or pushing changes.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` | Already modified | Scope cli-devin cwd to `lineageDir`; absolutize `skillFile` from the repository root |
| `specs/system-deep-loop/039-devin-lineage-write-scope/spec.md` | Create | Problem, scope, requirements, and verified acceptance criteria |
| `specs/system-deep-loop/039-devin-lineage-write-scope/plan.md` | Create | Delivery and verification plan for the recorded fix |
| `specs/system-deep-loop/039-devin-lineage-write-scope/tasks.md` | Create | Completed work ledger with concrete evidence |
| `specs/system-deep-loop/039-devin-lineage-write-scope/checklist.md` | Create | Verification checklist requested for this Level-1 packet |
| `specs/system-deep-loop/039-devin-lineage-write-scope/implementation-summary.md` | Create | Closeout record and known follow-up |

### Verified fix (record exactly)

1. Lineage dispatch uses `cwd: lineage.kind === 'cli-devin' ? lineageDir : process.cwd()`. Devin's `--sandbox` now OS-confines writes to the lineage directory while repository reads remain available.
2. `buildLoopPrompt` resolves `skillFile` with `path.resolve(process.cwd(), ...)`, allowing a leaf with the scoped cwd to read its contract file.

### Verification evidence

- `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` passes.
- One GLM-5.2-max / cli-devin research iteration through the patched runtime produced no `containment_violation`.
- That iteration touched zero runtime files and left its verification-time `git status` clean.
- The leaf completed genuine research: Cursor and Devin were confirmed MCP-only, and the shared vision-runtime core was already CLI-agnostic.
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:requirements -->
## 4. REQUIREMENTS

### P0 - Blockers (MUST complete)

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-001 | OS-enforce the cli-devin write boundary | cli-devin dispatch uses `lineageDir` as cwd |
| REQ-002 | Preserve leaf contract access | `skillFile` is absolute before scoped-cwd dispatch |
| REQ-003 | Preserve other executor behavior | non-cli-devin dispatch continues to use `process.cwd()` |
| REQ-004 | Prove syntax validity | `node --check fanout-run.cjs` passes |
| REQ-005 | Prove containment behavior | GLM-5.2-max / cli-devin iteration produces no containment violation and touches zero runtime files |

### P1 - Required (complete OR user-approved deferral)

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-P1 | Keep the fix isolated | Only the worktree's `fanout-run.cjs` contains the runtime change |
| REQ-P2 | Preserve verified research usefulness | The test leaf performs genuine research despite scoped cwd |
| REQ-P3 | Separate unrelated follow-up | `salvage_miss` is documented without being treated as a regression |
<!-- /ANCHOR:requirements -->

---

<!-- ANCHOR:success-criteria -->
## 5. SUCCESS CRITERIA

- [x] cli-devin cwd is lineage-scoped. Evidence: `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:2501`.
- [x] Leaf contract path is absolute. Evidence: `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:1089`.
- [x] Syntax validation passes. Evidence: `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`.
- [x] Patched GLM-5.2-max iteration has no containment violation or runtime-file writes. Evidence: `implementation-summary.md` Verification table.
- [x] Separate persistence issue remains follow-up-only. Evidence: `implementation-summary.md` KNOWN LIMITATIONS.
<!-- /ANCHOR:success-criteria -->

---

<!-- ANCHOR:risks -->
## 6. RISKS & DEPENDENCIES

| Type | Item | Impact | Mitigation |
|------|------|--------|------------|
| Risk | Shared primary runtime remains unchanged | The fix is not active there | Keep merge as a separate operator decision |
| Risk | Leaf exits without persisting `research.md` | Research artifact can be lost despite correct containment | Track as the separate `salvage_miss` follow-up |
| Dependency | Devin `--sandbox` scopes workspace writes to process cwd | Required for OS-level write confinement | Dispatch cli-devin with `cwd: lineageDir` |
| Dependency | Leaf skill contract remains readable | Required for valid deep-loop execution | Resolve `skillFile` before changing subprocess cwd |
<!-- /ANCHOR:risks -->
---

<!-- ANCHOR:questions -->
## 7. OPEN QUESTIONS

### Answered Questions
- **Q**: Is the shared primary runtime changed by this packet? **A**: No. The runtime edit is isolated to this worktree.
- **Q**: Is `salvage_miss` part of this fix? **A**: No. It is a known separate follow-up.

### Open Questions
- None.
<!-- /ANCHOR:questions -->
