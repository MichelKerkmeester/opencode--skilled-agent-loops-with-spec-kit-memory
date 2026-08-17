---
title: "Feature Specification: sk-vision MCP catalog and playbook coverage"
description: "Document the shared MCP transport and add deterministic standalone, Cursor, and Devin validation scenarios."
trigger_phrases:
  - "sk-vision MCP catalog coverage"
  - "sk-vision Cursor Devin playbook"
  - "VSN-017 VSN-018 VSN-019"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the catalog and playbook specification metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook/spec.md"
      - ".opencode/skills/sk-vision/feature-catalog/host-adapters/mcp-transport.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-004-catalog-and-playbook"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: sk-vision MCP catalog and playbook coverage

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
| **Parent Spec** | `../spec.md` |
| **Predecessor** | `003-devin-adapter` |
| **Successor** | None |
| **Handoff Criteria** | Catalog root/leaf parity and playbook root/scenario parity pass their document and package validators. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

The shared MCP server and both MCP-only host registrations are shipped. The research identified two remaining documentation gaps: a canonical host-adapter catalog entry and manual scenarios for standalone launch, Cursor attachment, and Devin attachment.

**Scope Boundary:** Update only `.opencode/skills/sk-vision/feature-catalog/`, `.opencode/skills/sk-vision/manual-testing-playbook/`, and this child documentation suite.
<!-- /ANCHOR:phase-context -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement

The current-state inventory names only the native adapters, and the manual corpus has no operator contract for the universal MCP fallback or its two hosts.

### Purpose

Add one catalog leaf for the shared transport, link it from the root, and add the next three stable VSN scenarios with exact commands, expected evidence, pass/fail criteria, and host-specific attach steps.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope

- Add `host-adapters/mcp-transport.md` and its root catalog entry.
- Describe Cursor and Devin as consumers of the same 13-tool stdio server.
- Add `VSN-017` for standalone `tools/list` -> 13.
- Add `VSN-018` for Cursor config, attach, and status.
- Add `VSN-019` for Devin config, attach, namespace, and status.
- Update root playbook summaries, automated-test anchors, and cross-reference index.

### Out of Scope

- Runtime or host-config behavior changes.
- Executing native Cursor or Devin GUI/cloud sessions.
- Renumbering published VSN IDs.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| `.opencode/skills/sk-vision/feature-catalog/feature-catalog.md` | Modify | Add MCP current-state entry |
| `.opencode/skills/sk-vision/feature-catalog/host-adapters/mcp-transport.md` | Create | Per-feature transport reference |
| `.opencode/skills/sk-vision/manual-testing-playbook/manual-testing-playbook.md` | Modify | Add MCP scenario directory entries and cross-references |
| `.opencode/skills/sk-vision/manual-testing-playbook/host-adapters/{mcp-standalone,cursor-mcp,devin-mcp}.md` | Create | VSN-017 through VSN-019 execution contracts |
| `004-catalog-and-playbook/*.md` | Create | Level-2 specification and closeout evidence |
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:requirements -->
## 4. REQUIREMENTS

### P0 - Blockers

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-001 | Catalog the shipped MCP transport | Root entry links to one per-feature file with source and validation anchors |
| REQ-002 | Cover standalone launch | `VSN-017` starts the built server and requires exactly 13 tools |
| REQ-003 | Cover Cursor attach | `VSN-018` validates merged JSON, connection, and `sk_vision_status` |
| REQ-004 | Cover Devin attach | `VSN-019` validates JSON, connection, and `mcp__sk-vision__sk_vision_status` |
| REQ-005 | Preserve package contracts | Document validators and strict playbook package validator pass |

### P1 - Required

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-P1 | Keep IDs stable and sequential | New IDs are `VSN-017`, `VSN-018`, and `VSN-019` |
| REQ-P2 | Link catalog and playbook | Every new scenario links to `mcp-transport.md`; root indexes link every new leaf |
| REQ-P3 | Avoid checkout literals in reusable scenario truth | Scenario commands derive absolute paths at execution time |
<!-- /ANCHOR:requirements -->

---

<!-- ANCHOR:success-criteria -->
## 5. SUCCESS CRITERIA

- [x] Catalog root and MCP leaf validate with zero issues. Evidence: `validate_document.py` returned `VALID` for both files.
- [x] Root playbook validates with zero issues. Evidence: `validate_document.py --type reference` returned `VALID`.
- [x] Playbook package passes fail-closed. Evidence: validator reports `scenarios=19`, `violations=0`, `warnings=0`, exit 0.
- [x] Three next-free IDs are present in root and leaves. Evidence: `VSN-017`, `VSN-018`, and `VSN-019` grep inventory.
<!-- /ANCHOR:success-criteria -->

---

<!-- ANCHOR:risks -->
## 6. RISKS & DEPENDENCIES

| Type | Item | Impact | Mitigation |
|------|------|--------|------------|
| Risk | Root/leaf drift | Package validator rejects the playbook | Update roots and leaves in the same change and run strict validation |
| Risk | Host scenario embeds one developer checkout | Corpus becomes non-portable | Derive the expected absolute path with `path.resolve` or `process.cwd()` |
| Dependency | Shipped MCP transport and configs | Docs could describe planned behavior | Anchor every claim to current implementation and parsed config evidence |
<!-- /ANCHOR:risks -->

---

<!-- ANCHOR:questions -->
## 7. OPEN QUESTIONS

None. The required catalog feature, scenarios, hosts, and IDs are implemented.
<!-- /ANCHOR:questions -->
