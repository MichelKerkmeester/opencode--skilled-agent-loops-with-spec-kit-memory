---
title: "Tasks: sk-vision MCP catalog and playbook coverage"
description: "Completed catalog, scenario authoring, validation, and closeout tasks for MCP-only hosts."
trigger_phrases:
  - "sk-vision MCP catalog and playbook tasks"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the catalog and playbook task evidence."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook/tasks.md"
      - ".opencode/skills/sk-vision/manual-testing-playbook/manual-testing-playbook.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-004-catalog-and-playbook"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: tasks-core | v2.2 -->
# Tasks: sk-vision MCP catalog and playbook coverage

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

**Task Format:** `T### [P?] Description (file path)`
<!-- /ANCHOR:notation -->

---

<!-- ANCHOR:phase-1 -->
## Phase 1: Setup

- [x] T001 Confirm downstream gaps. Evidence: `../research/research-report.md` section 5 names feature-catalog and manual-playbook coverage.
- [x] T002 Read sibling catalog leaves and playbook scenarios. Evidence: `.opencode/skills/sk-vision/feature-catalog/host-adapters/opencode-plugin.md` and `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/pi-extension.md`.
- [x] T003 Inventory scenario IDs. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/manual-testing-playbook.md` had published maximum `VSN-016`.
<!-- /ANCHOR:phase-1 -->

---

<!-- ANCHOR:phase-2 -->
## Phase 2: Implementation

- [x] T004 Add the MCP transport root entry and leaf. Evidence: `.opencode/skills/sk-vision/feature-catalog/feature-catalog.md` and `.opencode/skills/sk-vision/feature-catalog/host-adapters/mcp-transport.md`.
- [x] T005 Add standalone launch coverage. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/mcp-standalone.md` uses `VSN-017` and requires 13 tools.
- [x] T006 Add Cursor attach coverage. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/cursor-mcp.md` uses `VSN-018`.
- [x] T007 Add Devin attach coverage. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/devin-mcp.md` uses `VSN-019` and the `mcp__sk-vision__<tool>` namespace.
- [x] T008 Update root playbook parity. Evidence: `.opencode/skills/sk-vision/manual-testing-playbook/manual-testing-playbook.md` section 12 links all three leaves.
<!-- /ANCHOR:phase-2 -->

---

<!-- ANCHOR:phase-3 -->
## Phase 3: Verification

- [x] T009 Validate catalog documents. Evidence: both `validate_document.py` runs returned `VALID`, zero issues.
- [x] T010 Validate the root playbook. Evidence: `validate_document.py --type reference` returned `VALID`, zero issues.
- [x] T011 Repair first-run package findings. Evidence: `validate-playbook-package.cjs --package .opencode/skills/sk-vision/manual-testing-playbook` identified three `DEVELOPER_ABSOLUTE_PATH` findings.
- [x] T012 Pass strict package validation. Evidence: `validate-playbook-package.cjs --package .opencode/skills/sk-vision/manual-testing-playbook` returned `PASS package=sk-vision`, 19 scenarios, 5 categories, 0 violations, 0 warnings.
- [x] T013 Complete the Level-2 suite. Evidence: `spec.md`, `plan.md`, `tasks.md`, `checklist.md`, and `implementation-summary.md` exist.
<!-- /ANCHOR:phase-3 -->

---

<!-- ANCHOR:completion -->
## Completion Criteria

- [x] All tasks are complete. Evidence: T001-T013 are `[x]`.
- [x] No blocked tasks remain. Evidence: no `[B]` entries.
- [x] Catalog and playbook authoritative package checks pass. Evidence: `validate-playbook-package.cjs --package .opencode/skills/sk-vision/manual-testing-playbook` exits 0.
<!-- /ANCHOR:completion -->

---

<!-- ANCHOR:cross-refs -->
## Cross-References

- **Specification:** `spec.md`
- **Plan:** `plan.md`
- **Verification:** `checklist.md`
- **Closeout:** `implementation-summary.md`
<!-- /ANCHOR:cross-refs -->
