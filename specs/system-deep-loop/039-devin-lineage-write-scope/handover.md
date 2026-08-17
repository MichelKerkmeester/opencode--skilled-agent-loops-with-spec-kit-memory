---
title: "Handover: cli-devin lineage runtime fixes (write-scope + session-resume)"
description: "State of the two cli-devin deep-loop runtime fixes as of 2026-08-17: both shipped, unit- and e2e-verified, opened as PR #34 against skilled/v4.0.0.0. Written for the deep-loop-innovation session that shares fanout-run.cjs."
trigger_phrases:
  - "devin lineage runtime handover"
  - "cli-devin session resume handover"
  - "deep-loop runtime devin changes"
importance_tier: "important"
contextType: "planning"
parent: "system-deep-loop/039-devin-lineage-write-scope"
_memory:
  continuity:
    packet_pointer: "specs/system-deep-loop/039-devin-lineage-write-scope"
    last_updated_at: "2026-08-17T06:05:39.000Z"
    last_updated_by: "claude"
    recent_action: "Shipped and verified both cli-devin runtime fixes; opened PR #34 against skilled/v4.0.0.0."
    next_safe_action: "Coordinate with the 036-deep-loop-innovation session before reworking fanout-run.cjs dispatch."
    blockers: []
    completion_pct: 100
---

<!-- SPECKIT_TEMPLATE_SOURCE: handover-core | v2.2 -->
<!-- SPECKIT_LEVEL: 1 -->

# Handover: cli-devin Lineage Runtime Fixes

<!-- ANCHOR:handover-summary -->
## 1. Handover Summary

Two cli-devin fixes landed in the deep-loop fan-out runtime (`.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs`), both shipped and verified:

1. **Write-scope** (`67f551092b`) — cli-devin lineages run with `cwd = lineageDir`, so Devin `--sandbox` OS-confines writes to the bound lineage dir. `buildLoopPrompt` resolves `skillFile` absolutely so the scoped leaf still reads its contract.
2. **Session-resume** (`46ca2634ac`) — on retry, cli-devin dispatches `devin -c` (continue the prior session) instead of a fresh `devin -p`, so a low-capacity/free model's short turns accumulate toward a completed loop instead of restarting.

Both are recorded in this packet (`Complete`, `validate.sh --strict` 0/0), covered by `tests/unit/fanout-run.vitest.ts` (106/106), and confirmed end to end: a free-tier `glm-5-2` deep-review completed via resumed turns (`succeeded:1`) where the same config pre-fix `salvage_miss`ed 6 fresh restarts.

Everything is committed and pushed to `origin/worktrees/012-sk-vision` and opened as **PR #34** against `skilled/v4.0.0.0`: https://github.com/MichelKerkmeester/opencode--skilled-agent-loops-with-spec-kit-memory/pull/34 . Nothing is mid-write.
<!-- /ANCHOR:handover-summary -->

---

<!-- ANCHOR:context-transfer -->
## 2. Context Transfer

### 2.1 Key Decisions Made
| Decision | Rationale | Impact |
|----------|-----------|--------|
| Resume with `devin -c`, not `-r <session-id>` | `-c` continues the most-recent session **in the current directory**; the write-scope fix already gives each lineage its own cwd, so continue is unambiguous under parallel lineages — no fragile session-id capture/threading | `buildDevinLineageCommand` resume branch |
| Guard resume with a session-existence probe | A first attempt that opened no session has nothing to continue; fall back to a fresh `-p` rather than a failing `devin -c` | `devinLineageSessionExists` (`devin list --format json`, fail-safe, injectable) |
| Nudge, don't restate setup on resume | The resumed session already holds the loop context; restating the full prompt would invite a restart from `phase_init` | `buildDevinResumePrompt` |
| Scope only cli-devin cwd | cli-opencode/native already respected the prompt boundary | lineage dispatch `cwd` selection |

### 2.2 Files Modified
| File | Change | Status |
|------|--------|--------|
| `.opencode/skills/system-deep-loop/runtime/scripts/fanout-run.cjs` | cli-devin cwd confinement + absolute `skillFile` + resume branch + `buildDevinResumePrompt` + `devinLineageSessionExists` + `attempt` threading | Complete |
| `.opencode/skills/system-deep-loop/runtime/tests/unit/fanout-run.vitest.ts` | +5 tests (attempt-gating, resume, fallback, probe) | Complete |
| `specs/system-deep-loop/039-devin-lineage-write-scope/*` | Full packet, Complete, `--strict` 0/0 | Complete |

### 2.3 Traps & Scar Tissue
| Trap / blast site | Activation condition | Load-bearing or defensive? | How to avoid re-paying it |
|-------------------|----------------------|----------------------------|---------------------------|
| The two fixes are **coupled** | `-c` is directory-scoped; it only stays unambiguous under parallel lineages because each lineage has its own cwd | Load-bearing | If you change lineage cwd, resume can pick up a sibling's session — keep `cwd = lineageDir` for cli-devin |
| `attempt` threading `worker → buildLineageCommand → buildDevinLineageCommand` | Reworking dispatch/options plumbing drops `attempt` | Load-bearing | Attempt-gating fails **open** to fresh `-p` (silent), so a dropped `attempt` silently kills resume — preserve the thread |
| The `.devin` "mk devin hook could not resolve" message | A devin session whose `DEVIN_PROJECT_DIR`/cwd is not the repo root | Defensive (benign) | It is a soft additionalContext string; GLM handled it fine ("I'll treat that as context, proceeding"). Do **not** chase it — it did not derail the loop. Scoped fix if ever needed: set `DEVIN_PROJECT_DIR` in the dispatch env |
| `validate.sh` in the bare worktree returns 0 folders / stale-dist errors | Running the worktree's own validator | Defensive | Run the **main** checkout's `validate.sh`/generators pointed at the worktree packet path |
| Continuity freshness gate compares continuity-to-graph, not wall-clock | Adding a doc + regenerating graph while other docs keep an older timestamp | Defensive | Keep all continuity `last_updated_at` in sync when you regen `graph-metadata.json` |
<!-- /ANCHOR:context-transfer -->

---

<!-- ANCHOR:next-session -->
## 3. For Next Session

### 3.1 Recommended Starting Point — the `036-deep-loop-innovation` session
- **This is written for you.** You share `fanout-run.cjs`. Read PR #34's "For the 036-deep-loop-innovation session" section, then `buildDevinLineageCommand` in that file, before you rework dispatch or retry.
- **Relevance:** the resume pattern (keep read-context in one session, accumulate short turns) is directly usable for weak-model / cheap-executor loop completion — it overlaps `038-weak-model-loop-adherence` and any cost-reduction angle. Free-tier `glm-5-2` now completes a deep-review it never could before.
- **Next safe action:** coordinate merge order. Merging PR #34 is what lands these `fanout-run.cjs` changes in `v4`; until then they live only on `worktrees/012-sk-vision`. If your innovation work also edits `fanout-run.cjs`, sequence the merges to avoid conflicts.

### 3.2 Priority Tasks Remaining
1. **Operator decision:** merge PR #34 into `skilled/v4.0.0.0` (lands both cli-devin fixes) — or keep them branch-local.
2. Pre-existing caveat (sk-vision half of the PR): `vision-runtime/package.json` is gitignored, so the MCP SDK dep is uncommitted — the MCP transport won't build on a fresh checkout until resolved.
3. Optional: the same resume pattern could generalize to other short-turn executors (cli-opencode/cli-pi) if a similar `salvage_miss` shows up.

### 3.3 Critical Context to Load
- [ ] `spec.md` (§3 Verified fix, §4 Requirements), `implementation-summary.md` (Verification table + KNOWN LIMITATIONS)
- [ ] PR #34 body (the deep-loop-innovation call-out)
- [ ] `fanout-run.cjs`: `buildDevinLineageCommand`, `buildDevinResumePrompt`, `devinLineageSessionExists`, and the worker's `attempt` threading
<!-- /ANCHOR:next-session -->

---

<!-- ANCHOR:validation-checklist -->
## 4. Validation Checklist

- [x] All work committed and pushed (`origin/worktrees/012-sk-vision`, PR #34)
- [x] Continuity current (`implementation-summary.md` `_memory.continuity`, this handover)
- [x] No breaking changes mid-implementation (worktree clean, tree `succeeded:1` e2e)
- [x] Tests passing (`vitest` 106/106; packet `--strict` 0/0)
- [x] This handover complete
<!-- /ANCHOR:validation-checklist -->

---

<!-- ANCHOR:session-notes -->
## 5. Session Notes

Governance note carried from the session: the runtime logic was authored directly (not via an external CLI executor), deviating from the "orchestrate external executors" model. It is unit- and e2e-verified; re-authoring tested shared-runtime code through a free model is not recommended, but the option is open if consistency is preferred.

The negative-control evidence and the resumed-leaf stdout are snapshotted under the session scratchpad (`e2e-evidence-*`), not committed — the live `review/` run dirs were cleaned as test residue.
<!-- /ANCHOR:session-notes -->
