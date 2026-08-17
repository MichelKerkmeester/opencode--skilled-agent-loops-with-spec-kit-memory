---
title: "Implementation Plan: Devin sk-vision MCP adapter"
description: "Create Devin's repo-local MCP registration and verify the shared 13-tool launch contract."
trigger_phrases:
  - "Devin sk-vision MCP adapter plan"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/003-devin-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Devin adapter plan metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/003-devin-adapter/plan.md"
      - ".devin/mcp_config.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-003-devin-adapter"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: plan-core | v2.2 -->
# Implementation Plan: Devin sk-vision MCP adapter

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
| **Format** | Devin `mcpServers` JSON |
| **Transport** | MCP stdio |
| **Command** | `node` |
| **Tool Namespace** | `mcp__sk-vision__<tool>` |
| **Verification** | Node JSON assertion plus standalone MCP `tools/list` |

Create the missing project config with the smallest valid registration and point it at the already-built common server.
<!-- /ANCHOR:summary -->

---

<!-- ANCHOR:quality-gates -->
## 2. QUALITY GATES

### Definition of Ready

- [x] Devin host contract confirmed. Evidence: `../research/research-report.md` identifies `.devin/mcp_config.json` and namespacing.
- [x] Existing config absence confirmed. Evidence: `.devin/` had no `mcp_config.json` before implementation.
- [x] Built entry confirmed. Evidence: `vision-runtime/dist/mcp-server.js` exists.

### Definition of Done

- [x] Minimal repo config exists. Evidence: `.devin/mcp_config.json`.
- [x] Exact command and absolute argument are present. Evidence: parsed `skVision` object.
- [x] JSON and 13-tool process checks pass. Evidence: both Node commands exited 0.
<!-- /ANCHOR:quality-gates -->

---

<!-- ANCHOR:architecture -->
## 3. ARCHITECTURE

Devin -> `.devin/mcp_config.json` -> `node` -> `vision-runtime/dist/mcp-server.js` -> shared MCP registry -> `PhotonProvider` -> `RuntimeClient`.

Devin prefixes each advertised tool with `mcp__sk-vision__`, while the underlying MCP names remain `sk_vision_*`.
<!-- /ANCHOR:architecture -->

---

<!-- ANCHOR:phases -->
## 4. IMPLEMENTATION PHASES

### Phase 1: Inspect

- [x] Confirm the host contract, namespace, and absence of an existing project config.

### Phase 2: Configure

- [x] Create the minimal `mcpServers.sk-vision` registration.

### Phase 3: Verify and document

- [x] Parse and assert the JSON object.
- [x] Launch the configured built entry and observe 13 tools.
- [x] Record attach behavior and namespaced examples.
<!-- /ANCHOR:phases -->

---

<!-- ANCHOR:testing -->
## 5. TESTING STRATEGY

| Test Type | Scope | Evidence |
|-----------|-------|----------|
| JSON syntax | Entire `.devin/mcp_config.json` | `JSON.parse` succeeds |
| Registration shape | `mcpServers.sk-vision` | Command and absolute argument assertion exits 0 |
| Namespace documentation | Devin-facing references | `mcp__sk-vision__<tool>` recorded consistently |
| Transport smoke | Configured built entry | Official MCP client receives 13 tools |
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:dependencies -->
## 6. DEPENDENCIES

| Dependency | Status | Impact if Blocked |
|------------|--------|-------------------|
| Devin repo-local MCP loading | Confirmed by research | Project session does not attach the server |
| Node runtime | Available | Stdio process cannot start |
| Built MCP server | Available | No sk-vision tools are advertised |
<!-- /ANCHOR:dependencies -->

---

<!-- ANCHOR:rollback -->
## 7. ROLLBACK PLAN

Delete `.devin/mcp_config.json` if this repository-local registration must be removed. No runtime or other host configuration depends on this file.
<!-- /ANCHOR:rollback -->
