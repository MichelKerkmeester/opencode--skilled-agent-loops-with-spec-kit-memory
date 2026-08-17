---
title: "Implementation Summary: Devin lineage write scope"
description: "Closeout record for the verified cli-devin lineage write-containment fix."
trigger_phrases:
  - "Devin lineage write scope summary"
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
      - "specs/system-deep-loop/039-devin-lineage-write-scope/implementation-summary.md"
      - ".opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "system-deep-loop-039-devin-lineage-write-scope"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
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
| **Completed** | 2026-08-17 |
| **Level** | 1 |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:what-built -->
## What Was Built

The worktree-local fan-out runtime now OS-confines cli-devin writes to each bound lineage directory. The dispatch passes `lineageDir` as cwd only for cli-devin, while cli-opencode and native executors retain repository-root cwd. `buildLoopPrompt` resolves the deep-loop skill contract to an absolute path before dispatch so the scoped leaf can still read it.

### Fix evidence

| Edit | Artifact | Result |
|------|----------|--------|
| Absolute contract path | `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:1089` | Scoped-cwd leaf can resolve the deep-research or deep-review skill file |
| cli-devin cwd confinement | `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs:2501` | Devin `--sandbox` write scope matches the lineage directory |
<!-- /ANCHOR:what-built -->

---

<!-- ANCHOR:how-delivered -->
## How It Was Delivered

Two edits were applied to the worktree's `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`. Prompt construction first converts `skillFile` to an absolute repository-root path. Lineage dispatch then selects `lineageDir` as cwd only when `lineage.kind === 'cli-devin'`. This keeps the change executor-specific and leaves the existing containment guard in place as post-hoc detection.
<!-- /ANCHOR:how-delivered -->

---

<!-- ANCHOR:decisions -->
## Key Decisions

| Decision | Why |
|----------|-----|
| Scope only cli-devin cwd | cli-opencode and native executors already respected the prompt boundary |
| Use OS sandbox confinement | Prompt-only enforcement failed when GLM ignored the lineage boundary |
| Absolutize `skillFile` before dispatch | A lineage-scoped cwd must not break contract-file resolution |
| Keep `salvage_miss` separate | It concerns missing `research.md` persistence, not write containment |
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
| isolated blast radius | Worktree `fanout-run.cjs` only; shared primary runtime unchanged |
<!-- /ANCHOR:verification -->

---

<!-- ANCHOR:limitations -->
## KNOWN LIMITATIONS

- A separate `salvage_miss` remains: a leaf can exit without persisting `research.md`. This is not a regression from the write-containment fix and is not addressed by this packet.
- The runtime fix exists only in this worktree's `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`. The shared primary runtime is unchanged.
- Merging the fix into the shared primary runtime remains a separate operator decision.
- `description.json` and `graph-metadata.json` are intentionally absent because the conductor owns their generation.
<!-- /ANCHOR:limitations -->
