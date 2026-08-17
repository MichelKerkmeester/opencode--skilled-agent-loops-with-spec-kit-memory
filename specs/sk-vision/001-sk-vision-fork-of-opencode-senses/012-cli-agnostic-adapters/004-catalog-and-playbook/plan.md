---
title: "Implementation Plan: sk-vision MCP catalog and playbook coverage"
description: "Extend current-state inventory and deterministic manual validation for the shared MCP host path."
trigger_phrases:
  - "sk-vision MCP catalog and playbook plan"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the catalog and playbook plan metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/004-catalog-and-playbook/plan.md"
      - ".opencode/skills/sk-vision/manual-testing-playbook/manual-testing-playbook.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-004-catalog-and-playbook"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: plan-core | v2.2 -->
# Implementation Plan: sk-vision MCP catalog and playbook coverage

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

| Aspect | Value |
|--------|-------|
| **Catalog category** | `host-adapters` |
| **Catalog feature** | `mcp-transport.md` |
| **Scenario IDs** | `VSN-017`, `VSN-018`, `VSN-019` |
| **Validation** | sk-doc document validators and strict playbook package validator |

Follow existing OpenCode/Pi leaf formats, keep the catalog current-state focused, and put exact execution truth in three new playbook leaves.
<!-- /ANCHOR:summary -->

---

<!-- ANCHOR:quality-gates -->
## 2. QUALITY GATES

### Definition of Ready

- [x] Research gaps are explicit. Evidence: `../research/research-report.md` section 5.
- [x] Existing catalog leaf format is known. Evidence: `opencode-plugin.md` and `pi-extension.md`.
- [x] Next free IDs are confirmed. Evidence: prior playbook maximum was `VSN-016`.

### Definition of Done

- [x] Catalog root and leaf have parity. Evidence: root links `host-adapters/mcp-transport.md`.
- [x] Playbook root and three leaves have parity. Evidence: strict validator found 19 scenarios and zero violations.
- [x] Each scenario has exact prompt, sequence, signals, evidence, verdict, and triage. Evidence: the three new host-adapter leaves.
<!-- /ANCHOR:quality-gates -->

---

<!-- ANCHOR:architecture -->
## 3. ARCHITECTURE

- **Feature catalog root:** concise current-state inventory and navigation.
- **MCP transport leaf:** shared behavior, source anchors, host registrations, and validation anchors.
- **Playbook root:** package policy plus scenario summaries and cross-reference index.
- **Scenario leaves:** one deterministic execution contract per VSN ID.
<!-- /ANCHOR:architecture -->

---

<!-- ANCHOR:phases -->
## 4. IMPLEMENTATION PHASES

### Phase 1: Catalog

- [x] Add one shared MCP transport leaf and root entry.

### Phase 2: Playbook

- [x] Add standalone, Cursor, and Devin leaves with IDs 017-019.
- [x] Update root coverage, host preconditions, test anchors, and index.

### Phase 3: Validation

- [x] Run document validators on catalog root/leaf and playbook root.
- [x] Repair portable-path violations found by the first package run.
- [x] Rerun strict package validation to exit 0.
<!-- /ANCHOR:phases -->

---

<!-- ANCHOR:testing -->
## 5. TESTING STRATEGY

| Test Type | Scope | Result |
|-----------|-------|--------|
| Catalog document validation | Root and MCP leaf | Both `VALID`, zero issues |
| Playbook root validation | Root reference | `VALID`, zero issues |
| Playbook package contract | 19 scenario leaves | `PASS`, zero violations/warnings |
| Transport proof | Node-spawned MCP server | `tools/list` count 13 |
| Config proof | Cursor and Devin JSON | Parse/assert exit 0 |
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:dependencies -->
## 6. DEPENDENCIES

| Dependency | Status | Impact if Blocked |
|------------|--------|-------------------|
| Existing catalog/playbook formats | Available | New docs could fail package conventions |
| MCP integration test | Shipped | Catalog lacks automated validation anchor |
| Cursor and Devin configs | Implemented | Host scenarios lack current-state targets |
<!-- /ANCHOR:dependencies -->

---

<!-- ANCHOR:rollback -->
## 7. ROLLBACK PLAN

Remove the MCP root entry and leaf, remove the three VSN root entries and leaves, and restore the root coverage/test/index text. Runtime and host configurations remain independently functional.
<!-- /ANCHOR:rollback -->
