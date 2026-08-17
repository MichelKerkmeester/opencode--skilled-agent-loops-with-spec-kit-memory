---
title: "Implementation Summary: Devin lineage runtime fixes"
description: "Closeout record for two verified cli-devin lineage runtime fixes: write containment and session-resume-on-retry."
trigger_phrases:
  - "Devin lineage write scope summary"
  - "Devin session resume summary"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "specs/system-deep-loop/039-devin-lineage-write-scope"
    last_updated_at: "2026-08-17T05:39:50.000Z"
    last_updated_by: "claude"
    recent_action: "Confirmed end-to-end: a free-tier glm-5-2 deep-review completed via resumed turns."
    next_safe_action: "Optionally merge the isolated fanout-run.cjs fixes into the shared primary runtime."
    blockers: []
    key_files:
      - "specs/system-deep-loop/039-devin-lineage-write-scope/implementation-summary.md"
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
<!-- SPECKIT_TEMPLATE_SOURCE: impl-summary-core | v2.2 -->
# Implementation Summary

<!-- SPECKIT_LEVEL: 1 -->

---

<!-- ANCHOR:metadata -->
## Metadata

| Field | Value |
|-------|-------|
| **Spec Folder** | 039-devin-lineage-write-scope |
| **Status** | Complete |
| **Level** | 1 |

Both runtime fixes are unit-verified (106/106) and end-to-end confirmed: a free-tier glm-5-2 deep-review completed via resumed turns.
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:what-built -->
## What Was Built

The worktree-local fan-out runtime now does two things for cli-devin lineages. First, it OS-confines writes to each bound lineage directory: the dispatch passes `lineageDir` as cwd only for cli-devin, while cli-opencode and native executors retain repository-root cwd, and `buildLoopPrompt` resolves the deep-loop skill contract to an absolute path before dispatch so the scoped leaf can still read it. Second, it resumes the prior session on retry instead of restarting: when an attempt after the first finds an existing session in the lineage dir, it dispatches `devin -c` with a short "finish what you started" nudge so a low-capacity model's short turns accumulate toward a completed loop.

### Fix evidence

| Edit | Artifact | Result |
|------|----------|--------|
| Absolute contract path | `buildLoopPrompt` `skillFile = path.resolve(process.cwd(), ...)` | Scoped-cwd leaf can resolve the deep-research or deep-review skill file |
| cli-devin cwd confinement | `fanout-run.cjs` lineage dispatch `cwd: lineage.kind === 'cli-devin' ? lineageDir : process.cwd()` | Devin `--sandbox` write scope matches the lineage directory |
| Session resume on retry | `buildDevinLineageCommand` resume branch (`-c -p <nudge>` when `options.attempt > 1` and a session exists) | Free-tier short turns accumulate instead of restarting from `phase_init` |
| Resume nudge | `buildDevinResumePrompt` | Reuses the artifact names, lineage-dir write boundary, and `FANOUT_LINEAGE_COMPLETE` marker so a resumed leaf finishes rather than restarts |
| Session probe | `devinLineageSessionExists` (`devin list --format json`, fail-safe to false, injectable) | No-session or unreadable-list retries fall back to a fresh `-p` |
<!-- /ANCHOR:what-built -->

---

<!-- ANCHOR:how-delivered -->
## How It Was Delivered

The write-scope fix applied two edits to the worktree's `fanout-run.cjs`: prompt construction converts `skillFile` to an absolute repository-root path, and lineage dispatch selects `lineageDir` as cwd only when `lineage.kind === 'cli-devin'`. This keeps the change executor-specific and leaves the existing containment guard in place as post-hoc detection.

The session-resume fix threads the retry harness's `attempt` number into `buildLineageCommand`'s options so it reaches `buildDevinLineageCommand`. On `attempt > 1`, an injectable session probe (`devinLineageSessionExists`, defaulting to `devin list --format json` in the lineage dir) decides whether to continue: when a session exists the command becomes `['-c', '-p', <resume nudge>, '--model', model]`, otherwise it stays a fresh `['-p', <full prompt>, ...]`. The sandbox/permission flags are unchanged on both paths. `-c` was chosen over `-r <session-id>` because it is directory-scoped and the write-scope fix already gives each lineage its own cwd, so continue is unambiguous under parallel lineages without threading a captured session id.
<!-- /ANCHOR:how-delivered -->

---

<!-- ANCHOR:decisions -->
## Key Decisions

| Decision | Why |
|----------|-----|
| Scope only cli-devin cwd | cli-opencode and native executors already respected the prompt boundary |
| Use OS sandbox confinement | Prompt-only enforcement failed when GLM ignored the lineage boundary |
| Absolutize `skillFile` before dispatch | A lineage-scoped cwd must not break contract-file resolution |
| Resume with `-c`, not `-r <id>` | `-c` is directory-scoped and each lineage already owns its cwd, so continue is unambiguous under parallel lineages without threading a captured session id |
| Guard resume with a session probe | A first attempt that opened no session has nothing to continue; falling back to fresh `-p` avoids a failing `devin -c` |
| Nudge, don't restate setup | A resumed session already holds the loop context; restating the full prompt would invite a restart from `phase_init` |
| Make the probe injectable | Hermetic tests cannot spawn a real devin; `options.devinSessionProbe` lets them force each branch |
| Leave shared primary runtime unchanged | Integration is a separate operator decision |
<!-- /ANCHOR:decisions -->

---

<!-- ANCHOR:verification -->
## Verification

| Check | Result |
|-------|--------|
| `node --check .opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` | Pass |
| GLM-5.2-max / cli-devin research iteration | Completed through the patched runtime |
| containment guard result | No `containment_violation` |
| runtime-file writes | Zero; verification-time `git status` was clean |
| repository-read usefulness | Leaf confirmed Cursor and Devin are MCP-only |
| shared-core research | Leaf confirmed the shared vision-runtime core was already CLI-agnostic |
| `vitest run tests/unit/fanout-run.vitest.ts` | 106/106 pass (4 resume tests + 1 probe test added) |
| resume-on-retry test | A retry with an existing session builds `-c -p <nudge>`; the nudge contains `do NOT restart`, `review-report.md`, the lineage dir, and `FANOUT_LINEAGE_COMPLETE:<label>` |
| no-session fallback test | A retry with no session builds a fresh `-p <full prompt>` |
| attempt-1 test | The first attempt starts a fresh `-p` even when the probe reports an existing session |
| real-probe test | `devinLineageSessionExists` returns false for an empty dir and an empty path |
| e2e free-tier deep-review | PASS — `orchestration-summary.json` reports `succeeded:1, failed:0, salvage_miss:0`; attempt 3 (a `devin -c` resume) produced a 222-line `review-report.md` + `iterations/iteration-001..003.md` + full state |
| e2e negative control | Same config pre-fix: 6 fresh `-p` restarts, all `salvage_miss`, empty `iterations/`, 0 artifacts — isolating resume as the cause of the pass |
| e2e resume-carries-context | Resumed leaf log: "Resuming from where the previous turn stopped … I had completed context exploration and was writing the init state files when the turn was interrupted" |
| isolated blast radius | Worktree `fanout-run.cjs` + its unit test only; shared primary runtime unchanged |
<!-- /ANCHOR:verification -->

---

<!-- ANCHOR:limitations -->
## KNOWN LIMITATIONS

- The session-resume fix targets the free-tier accumulation cause of `salvage_miss` (short turns that never reach synthesis in one attempt). It is verified at the unit level (106/106) and end to end: a free-tier `glm-5-2` deep-review completed on attempt 3 (`succeeded:1`) via resumed turns, where the same config pre-fix salvage-missed 6 fresh restarts.
- Resume relies on the write-scope fix's cwd invariant: `devin -c` is directory-scoped, so if a future change stops running each lineage in its own cwd, continue could pick up a sibling lineage's session. The two fixes are coupled by design.
- The `.devin/hooks.v1.json` "mk devin hook could not resolve" message can appear when a devin session's `DEVIN_PROJECT_DIR`/cwd is not the repo root. It is a soft additionalContext string, not a hard failure, and the shared hooks file is intentionally left unchanged here; if it degrades live dispatches, the scoped fix is to set `DEVIN_PROJECT_DIR` in the devin dispatch env.
- The runtime fixes exist only in this worktree's `fanout-run.cjs` and its unit test. The shared primary runtime is unchanged; merging remains a separate operator decision.
- `description.json` and `graph-metadata.json` are regenerated by the conductor, not hand-authored.
<!-- /ANCHOR:limitations -->
