---
title: "Verification Checklist: sk-vision MCP catalog and playbook coverage"
description: "Verification Date: 2026-08-16"
trigger_phrases:
  - "sk-vision MCP catalog and playbook checklist"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the catalog and playbook checklist evidence."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook/checklist.md"
      - ".opencode/skills/sk-vision/feature-catalog/feature-catalog.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-004-catalog-and-playbook"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
# Verification Checklist: sk-vision MCP catalog and playbook coverage

<!-- SPECKIT_LEVEL: 2 -->
<!-- SPECKIT_TEMPLATE_SOURCE: checklist | v2.2 -->

---

<!-- ANCHOR:protocol -->
## Verification Protocol

| Priority | Handling | Completion Impact |
|----------|----------|-------------------|
| **[P0]** | HARD BLOCKER | Cannot claim done until complete |
| **[P1]** | Required | Must complete or receive approval |
| **[P2]** | Optional | May defer with a reason |
<!-- /ANCHOR:protocol -->

---

<!-- ANCHOR:pre-impl -->
## Pre-Implementation

- [x] CHK-001 [P0] Root entry exists. Evidence: `.opencode/skills/sk-vision/feature-catalog/feature-catalog.md` links `.opencode/skills/sk-vision/feature-catalog/host-adapters/mcp-transport.md`.
<!-- /ANCHOR:pre-impl -->

---

<!-- ANCHOR:code-quality -->
## Code Quality

- [x] CHK-002 [P0] Leaf follows the four-section format. Evidence: `.opencode/skills/sk-vision/feature-catalog/host-adapters/mcp-transport.md` has OVERVIEW, HOW IT WORKS, SOURCE FILES, and SOURCE METADATA.
- [x] CHK-003 [P0] Cursor and Devin usage is current-state text. Evidence: leaf names `.cursor/mcp.json` and `.devin/mcp_config.json`.
- [x] CHK-004 [P1] Source and validation anchors exist. Evidence: `.opencode/skills/sk-vision/vision-runtime/src/mcp/server.ts`, `.opencode/skills/sk-vision/vision-runtime/src/mcp/server.test.ts`, both configs, and three playbook leaves.
- [x] CHK-005 [P0] Catalog validation passes. Evidence: `validate_document.py` reports the root and leaf `VALID` with zero issues.
<!-- /ANCHOR:code-quality -->

---

<!-- ANCHOR:testing -->
## Testing

- [x] CHK-020 [P0] Root playbook validates. Evidence: `validate_document.py --type reference` returned `VALID`.
- [x] CHK-021 [P0] Strict package validator passes. Evidence: `validate-playbook-package.cjs --package .opencode/skills/sk-vision/manual-testing-playbook` returns `PASS package=sk-vision tier=FAIL_CLOSED scenarios=19 categories=5 operator=19 violations=0 warnings=0`.
- [x] CHK-022 [P1] Scenario truth is portable. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/mcp-standalone.md` uses `process.cwd()`; host checks use `path.resolve()`.
- [x] CHK-023 [P1] Root/leaf parity is complete. Evidence: `validate-playbook-package.cjs --package .opencode/skills/sk-vision/manual-testing-playbook` reports 19 operator scenarios with zero violations.
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:fix-completeness -->
## Fix Completeness

- [x] CHK-010 [P0] Next free IDs are used. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/manual-testing-playbook.md` records `VSN-017`, `VSN-018`, and `VSN-019` after `VSN-016`.
- [x] CHK-011 [P0] Standalone scenario requires exact inventory. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/mcp-standalone.md` passes only on 13 unique tools.
- [x] CHK-012 [P0] Cursor scenario covers attach. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/cursor-mcp.md` checks merge preservation, connection, and status.
- [x] CHK-013 [P0] Devin scenario covers attach and namespace. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/devin-mcp.md` calls `mcp__sk-vision__sk_vision_status`.
- [x] CHK-014 [P1] Prompt fields are synchronized. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/manual-testing-playbook.md` matches each leaf contract and execution prompt.
- [x] CHK-015 [P1] Every new scenario links the catalog. Evidence: all three leaves reference `.opencode/skills/sk-vision/feature-catalog/host-adapters/mcp-transport.md`.
<!-- /ANCHOR:fix-completeness -->

---

<!-- ANCHOR:security -->
## Security

- [x] CHK-030 [P0] No runtime or deep-loop files were changed. Evidence: `spec.md` limits this child to catalog, playbook, and spec documents.
<!-- /ANCHOR:security -->

---

<!-- ANCHOR:docs -->
## Documentation

- [x] CHK-031 [P1] Level-2 suite is complete. Evidence: `spec.md`, `plan.md`, `tasks.md`, `checklist.md`, and `implementation-summary.md` exist.
- [x] CHK-033 [P1] Spec-kit `validate.sh` was not run. Evidence: verification uses only the permitted sk-doc validators and direct runtime/config checks.
<!-- /ANCHOR:docs -->

---

<!-- ANCHOR:file-org -->
## File Organization

- [x] CHK-032 [P1] No JSON metadata was authored. Evidence: `checklist.md` is one of the requested Markdown files.
<!-- /ANCHOR:file-org -->

---

<!-- ANCHOR:summary -->
## Verification Summary

- [x] CHK-040 [P0] Every checklist item cites a concrete artifact. Evidence: all entries in `checklist.md` include backticked files or commands.
- [x] CHK-041 [P0] Catalog and playbook coverage are complete. Evidence: `validate-playbook-package.cjs --package .opencode/skills/sk-vision/manual-testing-playbook` exits 0.
<!-- /ANCHOR:summary -->
