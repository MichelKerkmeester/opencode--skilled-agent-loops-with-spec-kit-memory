---
title: "Tasks: Devin lineage runtime fixes"
description: "Task ledger for the cli-devin lineage write-containment and session-resume fixes."
trigger_phrases:
  - "Devin lineage write scope tasks"
  - "Devin session resume tasks"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "specs/system-deep-loop/039-devin-lineage-write-scope"
    last_updated_at: "2026-08-17T05:02:57.000Z"
    last_updated_by: "claude"
    recent_action: "Added and unit-verified cli-devin session-resume-on-retry to the lineage runtime."
    next_safe_action: "Run a free-tier glm-5-2 deep-review to confirm resumed turns produce the artifact."
    blockers: []
    key_files:
      - "specs/system-deep-loop/039-devin-lineage-write-scope/tasks.md"
      - ".opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs"
      - ".opencode/skills/system-deep-loop/runtime/tests/unit/fanout-run.vitest.ts"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "system-deep-loop-039-devin-lineage-write-scope"
      parent_session_id: null
    completion_pct: 85
    open_questions:
      - "Does a free-tier glm-5-2 deep-review's resumed turns produce review-report.md end-to-end?"
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: tasks-core | v2.2 -->
# Tasks: Devin lineage runtime fixes

<!-- SPECKIT_LEVEL: 1 -->

---

<!-- ANCHOR:notation -->
## Task Notation

| Prefix | Meaning |
|--------|---------|
| `[ ]` | Pending |
| `[x]` | Completed |
| `[P]` | Parallelizable |
| `[B]` | Blocked |

**Task Format**: `T### [P?] Description (file path)`
<!-- /ANCHOR:notation -->

---

<!-- ANCHOR:phase-1 -->
## Phase 1: Setup

- [x] T001 Confirm the violation and root cause are bounded to cli-devin cwd behavior. Evidence: `spec.md` Problem Statement and `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` dispatch diff.
<!-- /ANCHOR:phase-1 -->

---

<!-- ANCHOR:phase-2 -->
## Phase 2: Implementation

- [x] T002 Scope cli-devin process cwd to `lineageDir`. Evidence: `fanout-run.cjs` lineage dispatch `cwd: lineage.kind === 'cli-devin' ? lineageDir : process.cwd()`.
- [x] T003 Preserve repository-root cwd for other executors. Evidence: same dispatch expression's `process.cwd()` fallback for non-cli-devin kinds.
- [x] T004 Absolutize `skillFile` before scoped-cwd dispatch. Evidence: `buildLoopPrompt` `path.resolve(process.cwd(), ...)`.
- [x] T010 Thread the retry `attempt` number into `buildLineageCommand` options. Evidence: `fanout-run.cjs` worker passes `attempt` in the options object to `buildLineageCommand`.
- [x] T011 Resume the prior session on a cli-devin retry. Evidence: `buildDevinLineageCommand` builds `['-c', '-p', <nudge>, '--model', model]` when `options.attempt > 1` and a session exists.
- [x] T012 Add the resume nudge and the session-existence probe. Evidence: `buildDevinResumePrompt` and `devinLineageSessionExists` in `fanout-run.cjs`.
<!-- /ANCHOR:phase-2 -->

---

<!-- ANCHOR:phase-3 -->
## Phase 3: Verification

- [x] T005 Run the syntax check. Evidence: `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` passes.
- [x] T006 Run one GLM-5.2-max / cli-devin research iteration. Evidence: `implementation-summary.md` Verification table records no `containment_violation`.
- [x] T007 Confirm the leaf touched zero runtime files. Evidence: `implementation-summary.md` records the verification-time clean `git status`.
- [x] T008 Confirm the leaf still performs genuine research. Evidence: `implementation-summary.md` records the Cursor, Devin, and CLI-agnostic core findings.
- [x] T009 Address the persistence follow-up. Evidence: `implementation-summary.md` records the resume fix targeting the free-tier `salvage_miss` cause.
- [x] T013 Prove resume behavior with hermetic unit tests. Evidence: `vitest run tests/unit/fanout-run.vitest.ts` → 106/106, adding resume, fallback, attempt-1, and probe tests.
- [ ] T014 Confirm end to end that a free-tier `glm-5-2` deep-review's resumed turns persist `review-report.md`. Evidence: pending the live run.
<!-- /ANCHOR:phase-3 -->

---

<!-- ANCHOR:completion -->
## Completion Criteria

- [x] Code and unit tasks marked `[x]`. Evidence: `tasks.md` T001-T013.
- [ ] End-to-end task T014 complete. Evidence: pending the free-tier `glm-5-2` deep-review run.
- [x] No `[B]` blocked tasks remaining. Evidence: `tasks.md` contains no blocked task entry.
- [x] Unit and containment verification passed. Evidence: `implementation-summary.md` Verification table.
<!-- /ANCHOR:completion -->

---

<!-- ANCHOR:cross-refs -->
## Cross-References

- **Specification**: See `spec.md`
- **Plan**: See `plan.md`
<!-- /ANCHOR:cross-refs -->
