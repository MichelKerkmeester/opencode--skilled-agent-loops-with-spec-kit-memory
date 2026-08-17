---
title: "Tasks: sk-vision 011 live validation"
description: "Executable tasks and evidence for whole-surface live validation."
trigger_phrases:
  - "sk-vision 011 tasks"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/011-live-validation"
    last_updated_at: "2026-08-17T00:03:36.000Z"
    last_updated_by: "opencode"
    recent_action: "Completed T001-T012 with durable evidence."
    next_safe_action: "Conductor metadata generation and validation."
    blockers: []
    key_files:
      - "tasks.md"
      - "scratch/"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-011-live-validation"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: tasks-core | v2.2 -->
# Tasks: sk-vision 011 live validation

<!-- SPECKIT_LEVEL: 2 -->

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

- [x] T001 Map VSN-001/003-011/013 to the eleven unproven tools - evidence: phase 009 scenario table and per-feature files read (artifact: `011-live-validation/spec.md`).
- [x] T002 Read `runtime.py`, `tools.ts`, and `plugin.ts` for exact params and inspect composition - evidence: detect/point use `target`; diff uses `other`; inspect composes caption/scene/ocr.
- [x] T003 Create phase scratch and copied fixture-b - evidence: `scratch/fixture-b.png` exists and is a copy of the 480x140 fixture.
<!-- /ANCHOR:phase-1 -->

---

<!-- ANCHOR:phase-2 -->
## Phase 2: Implementation

- [x] T004 Run one main persistent NDJSON stream across inspect constituents and the ten direct runtime methods - evidence: response lines persisted in eleven transcript files (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] T005 Resolve detect ambiguity with one bounded retry - evidence: `text`/`ERROR` empty, `word` one normalized box in `live-vsn003-detect-transcript.txt`.
- [x] T006 Resolve point evidence with concrete target `word` - evidence: one normalized point in `live-vsn004-point-transcript.txt`.
- [x] T007 Classify segment as SKIP, not PASS or FAIL - evidence: exact `Model does not include a segment template` response in VSN-005 transcript.
- [x] T008 Persist all eleven outcome JSON files - evidence: 10 PASS, 1 SKIP, 0 FAIL tally (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] T009 Author seven-file full-surface benchmark report - evidence: report folder includes README, CSV, JSON, rendered report, source, failures, findings (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
<!-- /ANCHOR:phase-2 -->

---

<!-- ANCHOR:phase-3 -->
## Phase 3: Verification

- [x] T010 Verify generated artifacts - evidence: crop 240x70, zoom 960x280, annotate 480x140; annotation differs from source by 5.98% (artifact: `011-live-validation/scratch/live-vsn011-annotate.outcome.json`).
- [x] T011 Author five-file Level-2 phase suite and reconcile parent spec - evidence: only requested markdown files authored; parent phase map/order/handoffs/transitions include 011 (artifact: `011-live-validation/spec.md`).
- [x] T012 Run structural and scope checks without stale validators - evidence: JSON parse, frontmatter, inventory, forbidden-file, status, and diff checks recorded in `implementation-summary.md`.
<!-- /ANCHOR:phase-3 -->

---

<!-- ANCHOR:completion -->
## Completion Criteria

- [x] All tasks marked `[x]` - evidence: T001-T012 checked above.
- [x] No `[B]` blocked tasks remaining - evidence: segment is an accepted named SKIP, not an implementation blocker.
- [x] Manual verification passed - evidence: `implementation-summary.md` records 10 PASS, 1 SKIP, 0 FAIL.
<!-- /ANCHOR:completion -->

---

<!-- ANCHOR:cross-refs -->
## Cross-References

- **Specification**: See `spec.md`.
- **Plan**: See `plan.md`.
- **Aggregate report**: See `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/`.
<!-- /ANCHOR:cross-refs -->
