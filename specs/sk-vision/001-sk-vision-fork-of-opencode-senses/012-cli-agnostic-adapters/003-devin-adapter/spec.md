---
title: "Feature Specification: Devin sk-vision MCP adapter"
description: "Register the shipped sk-vision MCP stdio server for Devin with its documented tool namespace."
trigger_phrases:
  - "Devin sk-vision MCP adapter"
  - "Devin vision tools"
  - ".devin mcp config sk-vision"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/003-devin-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Devin adapter specification metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/003-devin-adapter/spec.md"
      - ".devin/mcp_config.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-003-devin-adapter"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: Devin sk-vision MCP adapter

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
| **Predecessor** | `002-cursor-adapter` |
| **Successor** | `004-catalog-and-playbook` |
| **Handoff Criteria** | `.devin/mcp_config.json` parses and registers `sk-vision` with the built stdio entry; the expected namespace is documented. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

The approved research in `../research/research-report.md` confirms that Devin loads project MCP servers from `.devin/mcp_config.json` and exposes tools as `mcp__<server>__<tool>`. The common transport already exposes all 13 canonical tool names.

**Scope Boundary:** Add repository-local Devin configuration and documentation only. Do not alter the server, runtime, model, or native host adapters.
<!-- /ANCHOR:phase-context -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement

Devin has no repo-local registration for the shipped sk-vision MCP process, so the 13 tools are not attached to project sessions.

### Purpose

Create `.devin/mcp_config.json` with an `mcpServers.sk-vision` stdio entry using Node and the absolute built-server path, then document Devin's `mcp__sk-vision__<tool>` naming.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope

- Create the missing repo-local Devin MCP config.
- Register `sk-vision` with command `node`.
- Pass one absolute argument to `dist/mcp-server.js`.
- Verify JSON syntax, object shape, and the shared 13-tool launch.
- Document namespaced Devin tool names.

### Out of Scope

- User-global `~/.config/devin/mcp_config.json`.
- Runtime, transport, schema, or model changes.
- Native Devin plugin code; Devin's supported integration is MCP.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| `.devin/mcp_config.json` | Create | Repository-local `sk-vision` MCP registration |
| `003-devin-adapter/*.md` | Create | Level-2 specification and closeout evidence |
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:requirements -->
## 4. REQUIREMENTS

### P0 - Blockers

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-001 | Use Devin's repository config shape | Root object contains `mcpServers.sk-vision` |
| REQ-002 | Register the built stdio server | Command is `node` and the sole argument is the absolute `dist/mcp-server.js` path |
| REQ-003 | Keep valid JSON | Node parses and structurally asserts `.devin/mcp_config.json` |

### P1 - Required

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-P1 | Document namespacing | Closeout states that Devin exposes `mcp__sk-vision__<tool>` names |
| REQ-P2 | Reuse the shared transport | No Devin-only runtime or schema copy is created |
<!-- /ANCHOR:requirements -->

---

<!-- ANCHOR:success-criteria -->
## 5. SUCCESS CRITERIA

- [x] Devin config parses and has one `sk-vision` server. Evidence: Node parse/assert printed `servers: ["sk-vision"]`.
- [x] Launch contract is exact. Evidence: `.devin/mcp_config.json` uses `node` and `/Users/michelkerkmeester/MEGA/Development/Code_Environment/Public/.worktrees/012-sk-vision/.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.
- [x] The process advertises all tools. Evidence: standalone MCP `tools/list` returned 13.
- [x] Tool namespace is explicit. Evidence: `implementation-summary.md` documents examples such as `mcp__sk-vision__sk_vision_status`.
<!-- /ANCHOR:success-criteria -->

---

<!-- ANCHOR:risks -->
## 6. RISKS & DEPENDENCIES

| Type | Item | Impact | Mitigation |
|------|------|--------|------------|
| Risk | Server key changes | Devin tool names change | Lock the key to `sk-vision` and document its namespace |
| Risk | Worktree path moves | Devin cannot spawn the process | Update the checkout-local absolute path after relocation |
| Dependency | Devin MCP project-config loading | No host attachment | Use the confirmed `.devin/mcp_config.json` contract |
| Dependency | Built MCP server | No vision tools | Launch directly with Node and assert 13 tools |
<!-- /ANCHOR:risks -->

---

<!-- ANCHOR:questions -->
## 7. OPEN QUESTIONS

None. The config path, server key, command, argument, and namespace were pre-approved.
<!-- /ANCHOR:questions -->
