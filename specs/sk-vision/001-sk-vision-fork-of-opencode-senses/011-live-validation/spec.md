---
title: "Feature Specification: sk-vision 011 live validation"
description: "Prove the remaining eleven public sk-vision tools against the local Moondream runtime with honest PASS, SKIP, or FAIL evidence."
trigger_phrases:
  - "sk-vision live validation"
  - "sk-vision full surface evidence"
  - "sk-vision remaining tools"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/011-live-validation"
    last_updated_at: "2026-08-17T00:03:36.000Z"
    last_updated_by: "opencode"
    recent_action: "Completed live validation of the eleven tools not previously proven."
    next_safe_action: "Parent completion; conductor may generate metadata and run validators on the main checkout."
    blockers: []
    key_files:
      - "spec.md"
      - "scratch/live-vsn001-inspect.outcome.json"
      - ".opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-011-live-validation"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions:
      - "Segment is an accepted SKIP because the default moondream2 checkpoint has no segment template."
      - "Reverse passed through the shipped local directory scan; no absent-index blocker occurred."
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: sk-vision 011 live validation

<!-- SPECKIT_LEVEL: 2 -->
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
| **Level** | 2 |
| **Priority** | P0 |
| **Status** | Complete |
| **Created** | 2026-08-16 |
| **Branch** | `worktrees/012-sk-vision` |
| **Parent Spec** | ../spec.md |
| **Predecessor** | 010-quality-gate |
| **Successor** | 012-cli-agnostic-adapters |
| **Handoff Criteria** | Every runnable tool among the eleven unproven public tools returns a correct live result; every SKIP has a named blocker; per-tool evidence and the aggregate report exist and parse. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

This is a **leaf phase** under the sk-vision packet root. Phase 009 had live evidence for only `ocr` and `status`; this phase closes the remaining public surface.

**Scope Boundary**: Live runtime execution and evidence for `inspect`, `detect`, `point`, `segment`, `metadata`, `crop`, `zoom`, `colors`, `diff`, `annotate`, and `reverse`; one aggregate benchmark report; this five-file spec suite; parent `spec.md` reconciliation. No runtime or adapter code changes.

**Dependencies**:
- 001-010 complete.
- Warm `~/.cache/sk-vision/venv` with moondream2 available on Apple Silicon MPS.
- Phase 009 fixture PNG available at the documented path.

**Deliverables**:
- One raw NDJSON transcript and one outcome JSON per tool under `scratch/`.
- Full-surface benchmark report under `.opencode/skills/sk-vision/benchmark/reports/`.
- Honest aggregate verdict and named blockers.
<!-- /ANCHOR:phase-context -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement
The skill exposes 13 public tools, but prior live evidence covered only VSN-002 `ocr` and VSN-012 `status`. Static tests and playbook contracts do not prove that the remaining handlers work with the installed local model and real fixture bytes.

### Purpose
Exercise the remaining eleven tools through the persistent NDJSON runtime, preserve the actual responses, and close the packet with an evidence-backed whole-surface verdict.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope
- Read each runtime handler and use its actual parameter keys.
- Resolve public `inspect` to its `caption` + `scene` + `ocr` composition.
- Load moondream2 once for the main sequence and call all remaining methods in the same stream.
- Perform bounded retries only when a more concrete target can distinguish fixture limitations from tool failure.
- Persist raw responses and machine-readable outcomes.
- Record PASS, SKIP, or FAIL without converting capability blockers into false passes.

### Out of Scope
- Editing runtime, adapter, playbook, catalog, or `context/` files.
- Forcing Moondream 3-only segmentation to pass on moondream2.
- Yandex network reverse search; the local provider is the deterministic scenario surface.
- Spec-kit validation in this bare worktree; the conductor owns that gate on the main checkout.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| `011-live-validation/{spec,plan,tasks,checklist,implementation-summary}.md` | Create | Level-2 phase suite |
| `011-live-validation/scratch/*` | Create | fixture-b, eleven transcripts, eleven outcome records |
| `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/*` | Create | Seven-file aggregate report |
| `../spec.md` | Modify | Add phase 011 and close stale transition prose |

### Live Results

| Scenario | Tool | Verdict | Evidence Summary |
|---|---|---|---|
| VSN-001 | inspect | PASS | caption, scene, and ocr all returned non-empty expected result types |
| VSN-003 | detect | PASS | bounded retry target `word` returned one normalized box |
| VSN-004 | point | PASS | target `word` returned one normalized point |
| VSN-005 | segment | SKIP | named blocker: moondream2 has no segment template |
| VSN-006 | colors | PASS | palette, buckets, and average RGB returned |
| VSN-007 | diff | PASS | copied fixture-b produced the correct zero-change result shape |
| VSN-008 | metadata | PASS | PNG, 480x140, RGB, 1284 bytes |
| VSN-009 | crop | PASS | existing 240x70 half-image output |
| VSN-010 | zoom | PASS | existing 960x280 output at 2x |
| VSN-011 | annotate | PASS | existing 480x140 output; 5.98% pixel change in annotated region |
| VSN-013 | reverse | PASS | local scan matched fixture-b at similarity 1.0 |

Aggregate report: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/`.
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:requirements -->
## 4. REQUIREMENTS

### P0 - Blockers (MUST complete)

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-001 | Validate all eleven remaining public tools live | Every tool has a transcript and outcome JSON |
| REQ-002 | Use real runtime contracts | Requests use handler keys from `runtime.py`; inspect uses its adapter composition |
| REQ-003 | Preserve honest verdicts | No fabricated output; SKIP names a concrete blocker; FAIL remains visible |
| REQ-004 | Publish aggregate evidence | Seven-file benchmark report exists and reflects all eleven outcomes |
| REQ-005 | Respect scope | Only phase 011, parent spec, and new benchmark report are changed by this phase |

### P1 - Required (complete OR user-approved deferral)

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-P1 | Verify generated artifacts | Crop, zoom, and annotate outputs exist with expected dimensions; annotation changes source pixels |
| REQ-P2 | Reconcile parent transitions | Parent map, order, directory status, handoffs, and stale phase-006 resume prose include 011 and show completion |
| REQ-P3 | Structural self-check | JSON parses, required files exist, frontmatter is present, and no forbidden metadata files are authored |
<!-- /ANCHOR:requirements -->

---

<!-- ANCHOR:success-criteria -->
## 5. SUCCESS CRITERIA

- [x] Eleven per-tool transcript/outcome pairs exist with 10 PASS, 1 named SKIP, and 0 FAIL.
- [x] Aggregate benchmark report contains the required seven files and the same tally.
- [x] Every runnable tool passed; segment is skipped only for the observed Moondream 2 capability blocker.
- [x] Parent spec reflects phases 001-011 as Complete and hands phase 011 to parent completion.
- [x] Structural checks replace the explicitly forbidden stale-worktree validator run.
<!-- /ANCHOR:success-criteria -->

---

<!-- ANCHOR:risks -->
## 6. RISKS & DEPENDENCIES

| Type | Item | Impact | Mitigation |
|------|------|--------|------------|
| Risk | Generic visual target returns no detections | Medium | One bounded retry with fixture-specific nouns; retain all responses |
| Risk | Model advertises a task it cannot execute | Medium | Treat observed template absence as SKIP with exact blocker |
| Risk | Generated cache paths are ephemeral | Low | Preserve dimensions and paths in raw evidence; keep source fixtures in packet scratch |
| Dependency | moondream2 cache and MPS runtime | Required | Record model/device observation; do not substitute fabricated output |
<!-- /ANCHOR:risks -->

---

<!-- ANCHOR:questions -->
## 7. OPEN QUESTIONS

### Answered Questions
- **Q**: Does public `inspect` map to one runtime method? **A**: No. Without a question, the OpenCode adapter composes `caption`, `scene`, and `ocr`; all three were run.
- **Q**: Must reverse SKIP without an index? **A**: No. The shipped handler scans the supplied directory and passed by matching fixture-b locally.
- **Q**: Is segment failure a packet failure? **A**: No. The execution brief explicitly accepts SKIP when moondream2 reports its missing segment template.

### Open Questions
- None.
<!-- /ANCHOR:questions -->
