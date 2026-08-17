---
title: "Feature Specification: Cursor sk-vision MCP adapter"
description: "Register the shipped sk-vision MCP stdio server in Cursor without disturbing existing MCP servers."
trigger_phrases:
  - "Cursor sk-vision MCP adapter"
  - "Cursor vision tools"
  - ".cursor mcp json sk-vision"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Cursor adapter specification metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter/spec.md"
      - ".cursor/mcp.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-002-cursor-adapter"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: Cursor sk-vision MCP adapter

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
| **Predecessor** | `001-mcp-server-transport` |
| **Successor** | `003-devin-adapter` |
| **Handoff Criteria** | `.cursor/mcp.json` parses, preserves its existing servers, and registers `sk-vision` with the built stdio entry. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

The approved research in `../research/research-report.md` confirms that Cursor loads external tools through `.cursor/mcp.json`. The preceding child shipped the common 13-tool MCP server at `.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.

**Scope Boundary:** Merge one Cursor registration. Do not alter the MCP server, native adapters, runtime core, or existing Cursor MCP registrations.
<!-- /ANCHOR:phase-context -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement

Cursor cannot discover the shipped sk-vision transport until the repository config declares how to launch it.

### Purpose

Add a repository-local `sk-vision` MCP server entry using `node` and the absolute built-server path while preserving every existing `mcpServers` entry.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope

- Read and merge `.cursor/mcp.json`.
- Add `mcpServers.sk-vision.command` as `node`.
- Add one absolute argument pointing to `dist/mcp-server.js`.
- Parse and structurally assert the final JSON.

### Out of Scope

- Cursor product settings outside this checkout.
- Runtime, transport, model, or native-adapter changes.
- Automatic image-attachment hooks, which MCP does not provide.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| `.cursor/mcp.json` | Merge | Preserve existing servers and add `sk-vision` |
| `002-cursor-adapter/*.md` | Create | Level-2 specification and closeout evidence |
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:requirements -->
## 4. REQUIREMENTS

### P0 - Blockers

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-001 | Preserve existing Cursor servers | `mk-spec-memory`, `mk_skill_advisor`, and `code_mode` remain unchanged |
| REQ-002 | Register sk-vision | `mcpServers.sk-vision` uses command `node` and exactly one absolute built-server argument |
| REQ-003 | Keep valid JSON | Node parses `.cursor/mcp.json` and asserts the registration |

### P1 - Required

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-P1 | Use the shared transport | The entry points to the shipped `dist/mcp-server.js`; no Cursor-specific adapter code is added |
| REQ-P2 | Record operator setup | `implementation-summary.md` contains the exact registration and attach expectation |
<!-- /ANCHOR:requirements -->

---

<!-- ANCHOR:success-criteria -->
## 5. SUCCESS CRITERIA

- [x] Existing Cursor MCP servers remain present. Evidence: parsed server keys are `mk-spec-memory`, `mk_skill_advisor`, `code_mode`, and `sk-vision`.
- [x] The new entry launches with `node`. Evidence: `.cursor/mcp.json` contains `mcpServers.sk-vision.command` set to `node`.
- [x] The argument is absolute and resolves to the shipped built server. Evidence: `.cursor/mcp.json` points to `/Users/michelkerkmeester/MEGA/Development/Code_Environment/Public/.worktrees/012-sk-vision/.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.
- [x] The built server advertises 13 tools when launched with Node. Evidence: standalone MCP client check returned `count: 13`.
<!-- /ANCHOR:success-criteria -->

---

<!-- ANCHOR:risks -->
## 6. RISKS & DEPENDENCIES

| Type | Item | Impact | Mitigation |
|------|------|--------|------------|
| Risk | Merge clobbers another server | Existing Cursor tooling disappears | Add only the `sk-vision` key and verify all prior keys remain |
| Risk | Worktree absolute path moves | Cursor cannot spawn the server | Treat this registration as checkout-local and update it when relocating the checkout |
| Dependency | Built MCP server | No tool surface for Cursor | Confirm the exact file exists and lists 13 tools through Node |
<!-- /ANCHOR:risks -->

---

<!-- ANCHOR:questions -->
## 7. OPEN QUESTIONS

None. The host model, config shape, server name, command, and argument were pre-approved.
<!-- /ANCHOR:questions -->
