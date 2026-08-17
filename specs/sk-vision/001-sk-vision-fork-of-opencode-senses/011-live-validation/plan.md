---
title: "Implementation Plan: sk-vision 011 live validation"
description: "Run the remaining tool surface through one warm NDJSON runtime, persist honest evidence, and reconcile the parent packet."
trigger_phrases:
  - "sk-vision live validation"
  - "sk-vision full surface evidence"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/011-live-validation"
    last_updated_at: "2026-08-17T00:03:36.000Z"
    last_updated_by: "opencode"
    recent_action: "Executed and documented the phase 011 live run."
    next_safe_action: "Conductor metadata generation and main-checkout validation."
    blockers: []
    key_files:
      - "spec.md"
      - "implementation-summary.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-011-live-validation"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: plan-core | v2.2 -->
# Implementation Plan: sk-vision 011 live validation

<!-- SPECKIT_LEVEL: 2 -->
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
| **Language/Stack** | Python 3.12.11 NDJSON runtime + JSON/Markdown evidence |
| **Framework** | sk-vision Moondream runtime on Apple Silicon MPS |
| **Storage** | Packet `scratch/` and skill benchmark reports |
| **Testing** | Live handler calls plus deterministic artifact and structural checks |

### Overview
Read the shipped handler and adapter contracts, load moondream2 once, call every remaining tool method with real fixture bytes, retry only one ambiguous visual target, classify outcomes honestly, and derive the report and phase docs from observed output.
<!-- /ANCHOR:summary -->

---

<!-- ANCHOR:quality-gates -->
## 2. QUALITY GATES

### Definition of Ready
- [x] Eleven-tool scope and scenario IDs mapped.
- [x] Runtime handler parameter keys confirmed from source.
- [x] Fixture and warm venv available.

### Definition of Done
- [x] All acceptance criteria met - evidence: `implementation-summary.md` verification table.
- [x] Spec, plan, tasks, checklist, report, and parent map synchronized.
<!-- /ANCHOR:quality-gates -->

---

<!-- ANCHOR:architecture -->
## 3. ARCHITECTURE

### Pattern
One warm model process receives line-delimited requests; each response is classified against the handler's documented shape and the manual scenario intent.

### Key Components
- **Adapter mapping**: public inspect becomes caption + scene + ocr.
- **Runtime handlers**: detect/point use `target`; diff uses `source` + `other`; deterministic tools use direct image transforms.
- **Evidence projection**: transcripts -> outcomes -> aggregate report -> phase closeout.

### Data Flow
Fixture(s) -> persistent runtime -> raw response lines -> per-tool verdicts -> aggregate report -> packet completion.
<!-- /ANCHOR:architecture -->

---

<!-- ANCHOR:affected-surfaces -->
## FIX ADDENDUM: AFFECTED SURFACES

| Surface | Current Role | Action | Verification |
|---------|--------------|--------|--------------|
| phase 011 scratch | raw evidence | create fixture-b, transcripts, outcomes | file inventory + JSON parse |
| benchmark report | aggregate evidence | create seven-file report | file inventory + JSON/CSV tally |
| phase 011 docs | Level-2 closeout | create five markdown files | frontmatter/anchor/self-check |
| parent spec | phase coordination | add 011 and close stale prose | targeted text scan |
<!-- /ANCHOR:affected-surfaces -->

---

<!-- ANCHOR:phases -->
## 4. IMPLEMENTATION PHASES

### Phase 1: Contract Mapping
- [x] Map VSN IDs to the eleven tools.
- [x] Read runtime handlers and OpenCode inspect composition.

### Phase 2: Live Execution
- [x] Create fixture-b and load moondream2.
- [x] Execute all methods in one main NDJSON stream.
- [x] Run bounded detect/point retries with concrete target nouns.

### Phase 3: Evidence And Closeout
- [x] Persist eleven transcripts and eleven outcomes.
- [x] Author the seven-file aggregate report.
- [x] Author phase docs and reconcile parent spec.
- [x] Run structural, JSON, artifact, scope, and diff checks.
<!-- /ANCHOR:phases -->

---

<!-- ANCHOR:testing -->
## 5. TESTING STRATEGY

| Test Type | Scope | Tools |
|-----------|-------|-------|
| Live model | inspect, detect, point, segment | persistent Python runtime |
| Deterministic image | metadata, crop, zoom, colors, diff, annotate, reverse | runtime handlers |
| Artifact | crop, zoom, annotate output | file existence + metadata + pixel diff |
| Structure | outcomes, report, phase suite | JSON parse, inventory, frontmatter scan |
| Scope | approved paths only | `git status` and `git diff` |
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:dependencies -->
## 6. DEPENDENCIES

| Dependency | Type | Status | Impact if Blocked |
|------------|------|--------|-------------------|
| phase 009 fixture | Internal | Available | No deterministic input |
| `~/.cache/sk-vision/venv/bin/python` | Runtime | Available | No live execution |
| moondream2 on MPS | Model | Available | Model-driven tools blocked |
| segment template | Model capability | Unavailable on moondream2 | Named SKIP for segment only |
<!-- /ANCHOR:dependencies -->

---

<!-- ANCHOR:rollback -->
## 7. ROLLBACK PLAN

- **Trigger**: Evidence is found to be malformed, misclassified, or outside approved scope.
- **Procedure**: Remove only phase-011-created evidence/report files and restore the parent spec changes. Runtime cache artifacts are disposable and do not alter the repository. No runtime code changed.
<!-- /ANCHOR:rollback -->
