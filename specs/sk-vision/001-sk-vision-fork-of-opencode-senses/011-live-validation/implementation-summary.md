---
title: "Implementation Summary: sk-vision 011 live validation"
description: "Closeout record for whole-surface live validation of the remaining sk-vision tools."
trigger_phrases:
  - "sk-vision 011 summary"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/011-live-validation"
    last_updated_at: "2026-08-17T00:03:36.000Z"
    last_updated_by: "opencode"
    recent_action: "011 complete with 10 PASS, 1 named SKIP, and 0 FAIL."
    next_safe_action: "Conductor generates description.json/graph-metadata.json and validates on the main checkout."
    blockers: []
    key_files:
      - "spec.md"
      - "scratch/"
      - ".opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.json"
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/spec.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-011-live-validation"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: impl-summary-core | v2.2 -->
# Implementation Summary

<!-- SPECKIT_LEVEL: 2 -->

---

<!-- ANCHOR:metadata -->
## Metadata

| Field | Value |
|-------|-------|
| **Spec Folder** | 011-live-validation |
| **Completed** | 2026-08-16 |
| **Level** | 2 |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:what-built -->
## What Was Built

Phase 011 added durable live-model evidence for every public sk-vision tool not already proven in phase 009. The phase contains one transcript and outcome per tool, a copied second fixture for diff/reverse, a seven-file aggregate benchmark report, a five-file Level-2 suite, and the parent phase-map reconciliation.

### Live Verdicts

| Tool | Scenario | Verdict | Observed Reason |
|---|---|---|---|
| inspect | VSN-001 | PASS | caption, scene, and ocr returned non-empty expected shapes |
| detect | VSN-003 | PASS | target `word` returned one normalized box after a bounded retry |
| point | VSN-004 | PASS | target `word` returned one normalized point |
| segment | VSN-005 | SKIP | moondream2 does not include a segment template |
| colors | VSN-006 | PASS | palette, buckets, average RGB, region count returned |
| diff | VSN-007 | PASS | copied fixture-b correctly returned zero changed pixels |
| metadata | VSN-008 | PASS | PNG, 480x140, RGB, 1284 bytes |
| crop | VSN-009 | PASS | existing 240x70 output and expected pixel bbox |
| zoom | VSN-010 | PASS | existing 960x280 output at scale 2.0 |
| annotate | VSN-011 | PASS | existing 480x140 output with 5.98% changed pixels |
| reverse | VSN-013 | PASS | local scan found fixture-b at similarity 1.0 |
<!-- /ANCHOR:what-built -->

---

<!-- ANCHOR:how-delivered -->
## How It Was Delivered

The runtime source was read before execution. One main Python process loaded moondream2 and accepted the full sequence over NDJSON stdin. Inspect used the exact OpenCode adapter composition. A bounded detect retry tested concrete targets after `text` returned no objects; `word` succeeded. Point was repeated with the same concrete noun to keep the final transcript concise. Deterministic handlers ran in the main stream. Outcomes and report files were authored from the observed response lines without changing runtime code.
<!-- /ANCHOR:how-delivered -->

---

<!-- ANCHOR:decisions -->
## Key Decisions

| Decision | Why |
|----------|-----|
| Classify segment as SKIP | The exact runtime error names a checkpoint capability blocker accepted by the brief |
| Pass reverse | The shipped handler performs a local directory scan and found fixture-b; no index blocker occurred |
| Pass zero-change diff | fixture-b is an allowed copy, so `changed_pct: 0.0` is the correct live result |
| Retain detect empty attempts | They are real evidence explaining why the bounded target retry was necessary |
| Do not run spec-kit validators | The brief explicitly reserves stale-worktree validation for the conductor |
<!-- /ANCHOR:decisions -->

---

<!-- ANCHOR:verification -->
## Verification

| Check | Result |
|-------|--------|
| Main live NDJSON sequence | 14 response envelopes; 13 results and one named segment capability error |
| Detect retry | `word` returned one normalized bbox; `number` returned two |
| Point retry | one normalized point at x=0.0938720703125, y=0.42236328125 |
| Per-tool evidence | eleven transcripts + eleven outcome JSON files |
| Outcome tally | 10 PASS, 1 SKIP, 0 FAIL |
| Crop artifact | exists; PNG RGB 240x70 |
| Zoom artifact | exists; PNG RGB 960x280 |
| Annotate artifact | exists; PNG RGB 480x140; 5.98% differs from source |
| Aggregate report | seven required files; JSON and CSV carry eleven rows |
| Phase suite | exactly five authored markdown files; no description.json or graph-metadata.json |
| Parent reconciliation | phase 011 map/order/status/hand-offs and completion prose updated |
| Spec-kit validator | NOT RUN by explicit instruction; conductor owns main-checkout validation |
<!-- /ANCHOR:verification -->

---

<!-- ANCHOR:limitations -->
## KNOWN LIMITATIONS

- Segment remains unvalidated on Moondream 3. The default moondream2 runtime returned `Model does not include a segment template`, so VSN-005 is SKIP.
- Detect is target-sensitive on this small text fixture. Generic `text` and literal `ERROR` returned no objects; `word` returned one box.
- Diff validates the two-source path and result shape with identical bytes. It does not prove changed-region localization because fixture-b is a copy.
- The model process emitted a harmless missing `MOONDREAM_API_KEY` warning and an asynchronous Photon telemetry shutdown warning on stderr. All NDJSON responses completed before process exit.
- Generated crop, zoom, and annotate files live in `~/.cache/sk-vision`; their observed paths and dimensions are preserved, but cache cleanup can remove them later.
- `description.json` and `graph-metadata.json` were intentionally not authored. The conductor generates them after this handoff.
<!-- /ANCHOR:limitations -->
