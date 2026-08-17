---
title: "Implementation Plan: Devin lineage runtime fixes"
description: "Record the cli-devin lineage write-containment and session-resume fanout edits and their verification."
trigger_phrases:
  - "Devin lineage write scope"
  - "cli-devin containment fix plan"
  - "Devin session resume plan"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "specs/system-deep-loop/039-devin-lineage-write-scope"
    last_updated_at: "2026-08-17T06:05:39.000Z"
    last_updated_by: "claude"
    recent_action: "Confirmed end-to-end: a free-tier glm-5-2 deep-review completed via resumed turns."
    next_safe_action: "Optionally merge the isolated fanout-run.cjs fixes into the shared primary runtime."
    blockers: []
    key_files:
      - "specs/system-deep-loop/039-devin-lineage-write-scope/plan.md"
      - ".opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs"
      - ".opencode/skills/system-deep-loop/runtime/tests/unit/fanout-run.vitest.ts"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "system-deep-loop-039-devin-lineage-write-scope"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions:
      - "A free-tier glm-5-2 deep-review completed via resumed turns (succeeded:1, review-report.md produced) where 6 fresh restarts had failed."
---
<!-- SPECKIT_TEMPLATE_SOURCE: plan-core | v2.2 -->
# Implementation Plan: Devin lineage runtime fixes

<!-- SPECKIT_LEVEL: 1 -->
<!--
SELF-CHECK:
- Confirm the plan names the simplest viable approach, affected surfaces, and verification path.
- Match phases to the stated scope; remove setup theater that does not change the outcome.
FAILURE MODES:
- Over-planning, missing rollback, and treating assumptions as dependencies.
-->

---

<!-- ANCHOR:summary -->
## 1. SUMMARY

### Technical Context

| Aspect | Value |
|--------|-------|
| **Language/Stack** | Node.js CommonJS runtime |
| **Framework** | system-deep-loop fan-out runtime and cli-devin executor |
| **Storage** | Lineage-local deep-loop artifacts |
| **Testing** | Node syntax check plus one GLM-5.2-max / cli-devin research iteration |

### Overview
Two coupled cli-devin runtime fixes. First, align Devin's OS sandbox with the existing lineage write boundary by changing only cli-devin subprocess cwd, and keep the leaf contract readable by making `skillFile` absolute. Second, resume the prior session on retry (`devin -c` + a short nudge, guarded by a session-existence probe) so a low-capacity model's short turns accumulate instead of restarting — the free-tier cause behind the `salvage_miss` follow-up. Record the containment and unit verification; the live free-tier end-to-end confirmation remains open.
<!-- /ANCHOR:summary -->

---

<!-- ANCHOR:quality-gates -->
## 2. QUALITY GATES

### Definition of Ready
- [x] Problem statement clear and scope documented. Evidence: `spec.md` sections 2-3.
- [x] Success criteria measurable. Evidence: `spec.md` section 5.
- [x] Dependencies identified. Evidence: `spec.md` section 6.

### Definition of Done
- [x] Code + unit acceptance criteria met. Evidence: `implementation-summary.md` Verification table (106/106 unit).
- [x] End-to-end free-tier resume confirmed. Evidence: e2e `orchestration-summary.json` → `succeeded:1`.
- [x] Docs updated (spec/plan/tasks/checklist/summary). Evidence: `spec.md`, `plan.md`, `tasks.md`, `checklist.md`, `implementation-summary.md`.
<!-- /ANCHOR:quality-gates -->

---

<!-- ANCHOR:architecture -->
## 3. ARCHITECTURE

### Pattern
Executor-specific cwd confinement at process spawn, paired with repository-root path resolution before dispatch.

### Key Components
- **Lineage dispatch**: cli-devin receives `lineageDir`; other executor kinds retain repository-root cwd.
- **Prompt construction**: the deep-research or deep-review `skillFile` path becomes absolute.
- **Containment verification**: the existing guard remains a detector, while Devin's sandbox becomes the preventative boundary.

### Data Flow
Repository-root prompt construction -> absolute skill contract path -> cli-devin spawn at `lineageDir` -> sandbox-confined lineage writes -> containment check.
<!-- /ANCHOR:architecture -->

---

<!-- ANCHOR:affected-surfaces -->
## FIX ADDENDUM: AFFECTED SURFACES

| Surface | Current Role | Action | Verification |
|---------|--------------|--------|--------------|
| `buildLoopPrompt` | Provides leaf contract path | resolve `skillFile` from repository root | absolute `path.resolve(process.cwd(), ...)` |
| lineage process spawn | Selects subprocess cwd | use `lineageDir` only for cli-devin | dispatch `cwd: lineage.kind === 'cli-devin' ? lineageDir : process.cwd()` |
| `buildDevinLineageCommand` | Builds the devin argv | resume with `-c` on retry when a session exists | resume branch + `buildDevinResumePrompt` + `devinLineageSessionExists` |
| lineage worker | Runs each attempt | thread `attempt` into `buildLineageCommand` options | worker options object carries `attempt` |
| `tests/unit/fanout-run.vitest.ts` | Unit coverage | add resume, fallback, attempt-1, probe tests | `vitest run` → 106/106 |
| shared primary runtime | Production/shared execution surface | no change | operator-controlled merge remains separate |
<!-- /ANCHOR:affected-surfaces -->

---

<!-- ANCHOR:phases -->
## 4. IMPLEMENTATION PHASES

### Phase 1: Confinement fix
- [x] Scope cli-devin subprocess cwd to the lineage directory. Evidence: dispatch `cwd: lineage.kind === 'cli-devin' ? lineageDir : process.cwd()`.
- [x] Keep cli-opencode and native cwd behavior unchanged. Evidence: the same expression's `process.cwd()` fallback.

### Phase 2: Contract-path fix
- [x] Resolve `skillFile` against repository-root cwd before dispatch. Evidence: `buildLoopPrompt` `path.resolve(process.cwd(), ...)`.

### Phase 3: Session-resume fix
- [x] Thread the retry `attempt` into `buildLineageCommand` options. Evidence: the lineage worker options object carries `attempt`.
- [x] Resume with `devin -c` + nudge when a session exists; else fresh `-p`. Evidence: `buildDevinLineageCommand` resume branch.
- [x] Add the resume nudge and injectable session probe. Evidence: `buildDevinResumePrompt` and `devinLineageSessionExists`.

### Phase 4: Verification and record
- [x] Pass Node syntax validation. Evidence: `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`.
- [x] Run one GLM-5.2-max / cli-devin research iteration. Evidence: `implementation-summary.md` Verification table.
- [x] Pass the unit suite. Evidence: `vitest run tests/unit/fanout-run.vitest.ts` → 106/106.
- [x] Confirm end-to-end free-tier resume. Evidence: e2e `orchestration-summary.json` → `succeeded:1`; attempt 3 (a `devin -c` resume) produced `review-report.md`.
<!-- /ANCHOR:phases -->

---

<!-- ANCHOR:testing -->
## 5. TESTING STRATEGY

| Test Type | Scope | Tools |
|-----------|-------|-------|
| Syntax | patched fan-out script | `node --check` |
| Containment | cli-devin lineage writes | GLM-5.2-max research iteration plus containment guard |
| Unit | resume/fallback/attempt-gating/probe | `vitest run tests/unit/fanout-run.vitest.ts` → 106/106 |
| Regression | non-cli-devin dispatch branch | conditional fallback to `process.cwd()` in `fanout-run.cjs` |
| E2E (pass) | free-tier resumed turns persist `review-report.md` | `glm-5-2` deep-review run → `succeeded:1` |
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:dependencies -->
## 6. DEPENDENCIES

| Dependency | Type | Status | Impact if Blocked |
|------------|------|--------|-------------------|
| Devin `--sandbox` workspace-write behavior | External executor | Verified | OS confinement would not match scoped cwd |
| `lineageDir` dispatch value | Internal | Available | No lineage-local cwd target |
| absolute deep-loop skill path | Internal | Implemented | Scoped leaf could not resolve its contract file |
<!-- /ANCHOR:dependencies -->

---

<!-- ANCHOR:rollback -->
## 7. ROLLBACK PLAN

- **Trigger**: The scoped cli-devin cwd blocks required repository reads, the resume path regresses non-devin executors, or `devin -c` picks up a wrong session.
- **Procedure**: Revert the worktree-local hunks in `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` (write-scope and/or resume) and the matching unit tests. The resume path is opt-in per attempt and gated on the session probe, so reverting only the resume hunks leaves the write-scope fix intact. The shared primary runtime is unchanged, so no shared-runtime rollback is required.
<!-- /ANCHOR:rollback -->
