---
title: "Feature Specification: Devin lineage runtime fixes"
description: "Record two verified cli-devin fan-out runtime fixes: OS-confined lineage write scope, and session-resume-on-retry so short free-tier turns accumulate to a completed loop."
trigger_phrases:
  - "Devin lineage write scope"
  - "cli-devin containment violation"
  - "fanout Devin sandbox cwd"
  - "Devin session resume on retry"
  - "cli-devin free tier accumulation"
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
      - "specs/system-deep-loop/039-devin-lineage-write-scope/spec.md"
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
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: Devin lineage runtime fixes

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
| **Handoff Criteria** | Both runtime fixes (write scope + session resume) are recorded, unit-verified (106/106), and end-to-end confirmed: a free-tier `glm-5-2` deep-review completed via resumed turns. Merging the isolated `fanout-run.cjs` into the shared primary runtime remains a separate operator decision. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

This is a **standard Level-1 packet** recording applied and verified cli-devin lineage runtime fixes.

**Scope Boundary**: Document the runtime edits in the worktree's `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` — the original write-scope fix and the session-resume-on-retry addition — their verified effect, and the accompanying unit tests. Do not modify the shared primary runtime or generate packet metadata by hand.

**Dependencies**:
- The worktree-local `fanout-run.cjs` contains all verified edits.
- The reported GLM-5.2-max / cli-devin research iteration completed without a containment violation.
- Devin's `-c` continues the most-recent session **in the current directory**, and each lineage already runs with cwd scoped to its lineage dir (the write-scope fix), so continue-on-retry is unambiguous under parallel lineages.

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

### Second problem: short free-tier turns never accumulate
The deep-loop retry harness re-runs the whole lineage worker on each attempt, rebuilding the full prompt and issuing a fresh `devin -p`. A low-capacity model — notably the free `glm-5-2` tier — takes very short turns and never reaches synthesis in a single turn. Because every retry restarted the loop from `phase_init`, each attempt discarded the prior turn's progress and the lineage never persisted its artifact. This surfaced as the `salvage_miss` follow-up in the original write-scope work: correct containment, but no `review-report.md` / `research.md` ever written.

### Purpose
Record the verified fixes that (1) align Devin's OS sandbox write boundary with the bound lineage directory while preserving access to the leaf contract file, and (2) resume the session the first attempt opened on each retry, so a low-capacity model's short turns accumulate toward a completed loop instead of restarting.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope
- Scope cli-devin lineage subprocesses to `cwd: lineageDir` while leaving other executors at `process.cwd()`.
- Resolve the deep-loop skill contract path from the repository root before dispatch.
- Resume the prior session on a cli-devin retry (`devin -c` with a short "finish what you started" nudge) instead of restarting, guarded by a directory-scoped session-existence probe with a fresh-start fallback.
- Add hermetic unit tests that lock the attempt-gating, resume-on-retry, no-session fallback, and probe fail-safe behavior.
- Record the GLM-5.2-max / cli-devin write-scope verification and the resume unit-test evidence.

### Out of Scope
- Merging the worktree-local change into the shared primary runtime.
- Changing cli-opencode or native executor cwd/retry behavior.
- Changing the shared `.devin/hooks.v1.json` infra file.
- Generating `description.json` or `graph-metadata.json` by hand.
- Committing or pushing changes.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` | Modified | Scope cli-devin cwd to `lineageDir`; absolutize `skillFile`; resume the prior session on retry via `devin -c` + a resume nudge, guarded by a session-existence probe |
| `.opencode/skills/system-deep-loop/runtime/tests/unit/fanout-run.vitest.ts` | Modified | Hermetic tests for attempt-gating, resume-on-retry, no-session fallback, and probe fail-safe |
| `specs/system-deep-loop/039-devin-lineage-write-scope/spec.md` | Update | Problem, scope, requirements, and verified acceptance criteria for both fixes |
| `specs/system-deep-loop/039-devin-lineage-write-scope/plan.md` | Create | Delivery and verification plan for the recorded fix |
| `specs/system-deep-loop/039-devin-lineage-write-scope/tasks.md` | Create | Completed work ledger with concrete evidence |
| `specs/system-deep-loop/039-devin-lineage-write-scope/checklist.md` | Create | Verification checklist requested for this Level-1 packet |
| `specs/system-deep-loop/039-devin-lineage-write-scope/implementation-summary.md` | Create | Closeout record and known follow-up |

### Verified fix (record exactly)

1. Lineage dispatch uses `cwd: lineage.kind === 'cli-devin' ? lineageDir : process.cwd()`. Devin's `--sandbox` now OS-confines writes to the lineage directory while repository reads remain available.
2. `buildLoopPrompt` resolves `skillFile` with `path.resolve(process.cwd(), ...)`, allowing a leaf with the scoped cwd to read its contract file.
3. `buildDevinLineageCommand` resumes on retry: when `options.attempt > 1` and a session already exists in the lineage dir, it builds `['-c', '-p', <resume nudge>, '--model', model]` instead of a fresh `['-p', <full prompt>, ...]`. The resume nudge (`buildDevinResumePrompt`) reuses the same artifact names, lineage-dir write boundary, and `FANOUT_LINEAGE_COMPLETE` marker as the full prompt, so the resumed leaf finishes rather than restarts.
4. `devinLineageSessionExists` probes `devin list --format json` in the lineage dir (directory-scoped, sub-second, no model round-trip) and fail-safes to "no session" on any error, so a first attempt that opened no session falls back to a fresh `-p` rather than a failing `devin -c`. The probe is injectable (`options.devinSessionProbe`) for hermetic tests.

### Verification evidence

- `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` passes.
- One GLM-5.2-max / cli-devin research iteration through the patched runtime produced no `containment_violation`.
- That iteration touched zero runtime files and left its verification-time `git status` clean.
- The leaf completed genuine research: Cursor and Devin were confirmed MCP-only, and the shared vision-runtime core was already CLI-agnostic.
- `vitest run tests/unit/fanout-run.vitest.ts` passes 106/106 (adds 4 resume tests + 1 probe test). The resume test asserts a retry with an existing session builds `-c -p <nudge>` where the nudge contains `do NOT restart`, `review-report.md`, the lineage dir, and `FANOUT_LINEAGE_COMPLETE:<label>`; the fallback test asserts a retry with no session builds a fresh `-p <full prompt>`; the attempt-1 test asserts the first attempt never resumes even when a session exists.
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
| REQ-006 | Resume the session on retry | A cli-devin retry with an existing session dispatches `devin -c` with a resume nudge, not a fresh full-prompt restart |
| REQ-007 | Fail safe when no session exists | A retry with no prior session, or an unreadable session list, falls back to a fresh `devin -p` |
| REQ-008 | Never resume the first attempt | Attempt 1 always starts a fresh session even when the probe reports an existing one |
| REQ-009 | Prove resume behavior with tests | `vitest run tests/unit/fanout-run.vitest.ts` passes 106/106 including the resume, fallback, attempt-1, and probe tests |

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

- [x] cli-devin cwd is lineage-scoped. Evidence: `fanout-run.cjs` lineage dispatch `cwd: lineage.kind === 'cli-devin' ? lineageDir : process.cwd()`.
- [x] Leaf contract path is absolute. Evidence: `buildLoopPrompt` resolves `skillFile` with `path.resolve(process.cwd(), ...)`.
- [x] Syntax validation passes. Evidence: `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`.
- [x] Patched GLM-5.2-max iteration has no containment violation or runtime-file writes. Evidence: `implementation-summary.md` Verification table.
- [x] Retry resumes the prior session. Evidence: `buildDevinLineageCommand` resume branch + the resume-on-retry test in `tests/unit/fanout-run.vitest.ts`.
- [x] No-session and probe-failure fall back to a fresh start. Evidence: the fallback test + the real-probe test in `tests/unit/fanout-run.vitest.ts`.
- [x] Resume behavior proven by tests. Evidence: `vitest run tests/unit/fanout-run.vitest.ts` → 106/106.
- [x] End-to-end: a free-tier `glm-5-2` deep-review's resumed turns produce `review-report.md`. Evidence: the e2e run's `orchestration-summary.json` reports `succeeded:1, failed:0, salvage_miss:0`; attempt 3 (a `devin -c` resume) wrote a 222-line `review-report.md` plus `iterations/iteration-001..003.md` and the full state set, where the pre-fix negative control salvage-missed 6 fresh restarts with an empty `iterations/`.
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
- **Q**: Should retries use `-c` (continue) or `-r <session-id>` (resume specific)? **A**: `-c`. It is directory-scoped, and the write-scope fix already runs each lineage with cwd = its lineage dir, so continue is unambiguous under parallel lineages without the fragility of parsing and threading a session id.
- **Q**: Do a free-tier `glm-5-2` deep-review's resumed turns accumulate to a persisted `review-report.md` end to end? **A**: Yes. The e2e run completed on attempt 3 (`succeeded:1`); the resumed leaf's own log confirmed it continued ("Resuming from where the previous turn stopped … I had completed context exploration").

### Open Questions
- None. (Merging the isolated fix into the shared primary runtime is a separate operator decision, tracked as out of scope.)
<!-- /ANCHOR:questions -->
