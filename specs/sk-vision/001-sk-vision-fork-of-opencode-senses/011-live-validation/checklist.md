---
title: "Verification Checklist: sk-vision 011 live validation"
description: "Verification Date: 2026-08-16"
trigger_phrases:
  - "sk-vision 011 checklist"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/011-live-validation"
    last_updated_at: "2026-08-17T00:03:36.000Z"
    last_updated_by: "opencode"
    recent_action: "Verified phase 011 evidence and scope."
    next_safe_action: "Conductor metadata generation and validation."
    blockers: []
    key_files:
      - "checklist.md"
      - "implementation-summary.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-011-live-validation"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
# Verification Checklist: sk-vision 011 live validation

<!-- SPECKIT_LEVEL: 2 -->
<!-- SPECKIT_TEMPLATE_SOURCE: checklist | v2.2 -->

---

<!-- ANCHOR:protocol -->
## Verification Protocol

| Priority | Handling | Completion Impact |
|----------|----------|-------------------|
| **[P0]** | HARD BLOCKER | Cannot claim done until complete |
| **[P1]** | Required | Must complete OR get user approval |
| **[P2]** | Optional | Can defer with documented reason |
<!-- /ANCHOR:protocol -->

---

<!-- ANCHOR:pre-impl -->
## Pre-Implementation

- [x] CHK-001 [P0] Requirements documented in spec.md. Evidence: REQ-001 through REQ-005 and REQ-P1 through REQ-P3 (artifact: `011-live-validation/spec.md`).
- [x] CHK-002 [P0] Technical approach defined in plan.md. Evidence: one warm NDJSON process and evidence projection described (artifact: `011-live-validation/plan.md`).
- [x] CHK-003 [P1] Dependencies identified and available. Evidence: Python 3.12.11, fixture, cached moondream2, and MPS observed (artifact: `011-live-validation/scratch/fixture-b.png`).
<!-- /ANCHOR:pre-impl -->

---

<!-- ANCHOR:code-quality -->
## Code Quality

- [x] CHK-010 [P0] No production code changed. Evidence: phase scope contains evidence/docs only; final scoped diff inspected (artifact: `011-live-validation/spec.md`).
- [x] CHK-011 [P0] Runtime errors classified honestly. Evidence: segment error preserved verbatim as SKIP; no response rewritten (artifact: `011-live-validation/scratch/live-vsn005-segment.outcome.json`).
- [x] CHK-012 [P1] Handler parameters follow shipped source. Evidence: `target`, `source`, `other`, `bbox`, `region`, `boxes`, and providers checked against `runtime.py`.
- [x] CHK-013 [P1] Adapter semantics preserved. Evidence: inspect validated as caption + scene + ocr, matching `tools.ts`.
<!-- /ANCHOR:code-quality -->

---

<!-- ANCHOR:testing -->
## Testing

- [x] CHK-020 [P0] All eleven tools executed live. Evidence: eleven transcript/outcome pairs in `scratch/`.
- [x] CHK-021 [P0] Every runnable tool passed. Evidence: aggregate tally 10 PASS, 1 accepted SKIP, 0 FAIL (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] CHK-022 [P1] Ambiguous target edge case tested. Evidence: detect transcript includes generic empty results and successful `word` retry.
- [x] CHK-023 [P1] Capability error scenario validated. Evidence: segment reports missing moondream2 segment template (artifact: `011-live-validation/scratch/live-vsn005-segment.outcome.json`).
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:fix-completeness -->
## Fix Completeness

- [x] CHK-FIX-001 [P0] Finding class: `live-runtime-validation`. Evidence: no remediation was needed; this phase proves behavior.
- [x] CHK-FIX-002 [P0] Same-class producer inventory. Evidence: all eleven unproven public tools listed in spec results table (artifact: `011-live-validation/spec.md`).
- [x] CHK-FIX-003 [P0] Consumer inventory. Evidence: outcomes feed report JSON/CSV, phase summary, and parent completion (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] CHK-FIX-004 [P0] Adversarial cases captured. Evidence: empty generic detect, unsupported segment, identical-image diff, local reverse (artifact: `011-live-validation/scratch/live-vsn003-detect.outcome.json`).
- [x] CHK-FIX-005 [P1] Matrix axes listed. Evidence: scenario x tool x verdict table in spec and report (artifact: `011-live-validation/spec.md`).
- [x] CHK-FIX-006 [P1] Host environment recorded. Evidence: Python 3.12.11, moondream2, MPS, venv executor (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] CHK-FIX-007 [P1] Evidence pinned. Evidence: report rows link repo-relative transcript paths (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
<!-- /ANCHOR:fix-completeness -->

---

<!-- ANCHOR:security -->
## Security

- [x] CHK-030 [P0] No secrets persisted. Evidence: harmless missing API-key warning only; no token value exists in evidence (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] CHK-031 [P0] Inputs remain local and bounded. Evidence: fixture paths and local reverse provider only; no Yandex upload (artifact: `011-live-validation/scratch/live-vsn013-reverse.outcome.json`).
- [x] CHK-032 [P1] No privileged operation used. Evidence: local venv runtime, cache outputs, and repo docs only (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
<!-- /ANCHOR:security -->

---

<!-- ANCHOR:docs -->
## Documentation

- [x] CHK-040 [P1] Spec/plan/tasks synchronized. Evidence: all carry 10 PASS, 1 SKIP, 0 FAIL and the same report path (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] CHK-041 [P1] Evidence limitations explicit. Evidence: segment blocker, detect retry, copied diff fixture, and stderr warnings documented (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
- [x] CHK-042 [P2] Parent packet updated. Evidence: phase 011 map/order/status/handoffs and completed transition prose (artifact: `specs/sk-vision/001-sk-vision-fork-of-opencode-senses/spec.md`).
<!-- /ANCHOR:docs -->

---

<!-- ANCHOR:file-org -->
## File Organization

- [x] CHK-050 [P1] Raw evidence is under phase scratch. Evidence: fixture-b plus 22 transcript/outcome files only (artifact: `011-live-validation/scratch/fixture-b.png`).
- [x] CHK-051 [P1] Curated evidence is under the new report folder. Evidence: exactly seven required report files (artifact: `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`).
<!-- /ANCHOR:file-org -->

---

<!-- ANCHOR:summary -->
## Verification Summary

- [x] CHK-060 [P0] All checklist items marked `[x]` with evidence. Evidence: every item above cites an artifact; aggregate report `.opencode/skills/sk-vision/benchmark/reports/2026-08-16--manual-testing-playbook--full-surface-live-run/skill-benchmark-report.md`.
- [x] CHK-062 [P0] Structural self-check passes without forbidden validator execution. Evidence: final JSON, inventory, frontmatter, status, and scoped-diff checks in `implementation-summary.md`.
<!-- /ANCHOR:summary -->
