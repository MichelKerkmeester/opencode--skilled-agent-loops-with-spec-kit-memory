---
title: "Implementation Plan: Devin lineage write scope"
description: "Record the two worktree-local fanout edits and their successful cli-devin containment verification."
trigger_phrases:
  - "Devin lineage write scope"
  - "cli-devin containment fix plan"
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
      - "specs/system-deep-loop/039-devin-lineage-write-scope/plan.md"
      - ".opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "system-deep-loop-039-devin-lineage-write-scope"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: plan-core | v2.2 -->
# Implementation Plan: Devin lineage write scope

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
Align Devin's OS sandbox with the existing lineage write boundary by changing only cli-devin subprocess cwd, then keep the leaf contract readable by making `skillFile` absolute. Record the already completed verification without expanding into the separate `salvage_miss` issue or primary-runtime integration.
<!-- /ANCHOR:summary -->

---

<!-- ANCHOR:quality-gates -->
## 2. QUALITY GATES

### Definition of Ready
- [x] Problem statement clear and scope documented. Evidence: `spec.md` sections 2-3.
- [x] Success criteria measurable. Evidence: `spec.md` section 5.
- [x] Dependencies identified. Evidence: `spec.md` section 6.

### Definition of Done
- [x] All acceptance criteria met. Evidence: `implementation-summary.md` Verification table.
- [x] Docs updated (spec/plan/tasks). Evidence: `spec.md`, `plan.md`, and `tasks.md`.
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
| `buildLoopPrompt` | Provides leaf contract path | resolve `skillFile` from repository root | `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:1089` |
| lineage process spawn | Selects subprocess cwd | use `lineageDir` only for cli-devin | `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:2501` |
| shared primary runtime | Production/shared execution surface | no change | operator-controlled merge remains separate |
<!-- /ANCHOR:affected-surfaces -->

---

<!-- ANCHOR:phases -->
## 4. IMPLEMENTATION PHASES

### Phase 1: Confinement fix
- [x] Scope cli-devin subprocess cwd to the lineage directory. Evidence: `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:2501`.
- [x] Keep cli-opencode and native cwd behavior unchanged. Evidence: `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:2501` fallback.

### Phase 2: Contract-path fix
- [x] Resolve `skillFile` against repository-root cwd before dispatch. Evidence: `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:1089`.

### Phase 3: Verification and record
- [x] Pass Node syntax validation. Evidence: `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`.
- [x] Run one GLM-5.2-max / cli-devin research iteration. Evidence: `implementation-summary.md` Verification table.
- [x] Record `salvage_miss` as a separate follow-up. Evidence: `implementation-summary.md` KNOWN LIMITATIONS.
<!-- /ANCHOR:phases -->

---

<!-- ANCHOR:testing -->
## 5. TESTING STRATEGY

| Test Type | Scope | Tools |
|-----------|-------|-------|
| Syntax | patched fan-out script | `node --check` |
| Containment | cli-devin lineage writes | GLM-5.2-max research iteration plus containment guard |
| Regression | non-cli-devin dispatch branch | conditional fallback to `process.cwd()` in `fanout-run.cjs` |
| Usefulness | scoped leaf can read contract and research repository | observed research output from the patched iteration |
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

- **Trigger**: The scoped cli-devin cwd blocks required repository reads or causes a runtime regression.
- **Procedure**: Revert the two worktree-local hunks in `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`. The shared primary runtime is unchanged, so no shared-runtime rollback is required.
<!-- /ANCHOR:rollback -->
