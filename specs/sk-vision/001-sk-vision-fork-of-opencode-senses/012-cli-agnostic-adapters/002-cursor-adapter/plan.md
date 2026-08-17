---
title: "Implementation Plan: Cursor sk-vision MCP adapter"
description: "Merge one stdio server registration into Cursor and verify preservation plus launchability."
trigger_phrases:
  - "Cursor sk-vision MCP adapter plan"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Cursor adapter plan metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter/plan.md"
      - ".cursor/mcp.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-002-cursor-adapter"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: plan-core | v2.2 -->
# Implementation Plan: Cursor sk-vision MCP adapter

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
| **Format** | Cursor `mcpServers` JSON |
| **Transport** | MCP stdio |
| **Command** | `node` |
| **Verification** | Node JSON assertion plus standalone MCP `tools/list` |

Merge the `sk-vision` entry into the existing object rather than replacing the file. Point it directly at the built transport delivered by the preceding child.
<!-- /ANCHOR:summary -->

---

<!-- ANCHOR:quality-gates -->
## 2. QUALITY GATES

### Definition of Ready

- [x] Cursor host contract confirmed. Evidence: `../research/research-report.md` identifies `.cursor/mcp.json` and `mcpServers`.
- [x] Existing config inventoried. Evidence: `.cursor/mcp.json` contained three servers before the merge.
- [x] Built entry confirmed. Evidence: `.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js` exists.

### Definition of Done

- [x] Existing entries are preserved. Evidence: all three original server keys remain.
- [x] `sk-vision` has the approved command and absolute argument. Evidence: `.cursor/mcp.json`.
- [x] JSON and transport checks pass. Evidence: Node parse/assert and MCP `tools/list` returned 13.
<!-- /ANCHOR:quality-gates -->

---

<!-- ANCHOR:architecture -->
## 3. ARCHITECTURE

Cursor -> `.cursor/mcp.json` -> `node` -> `vision-runtime/dist/mcp-server.js` -> shared MCP registry -> `PhotonProvider` -> `RuntimeClient`.

This child adds configuration only. Because the original Cursor path was a symlink to the shared Claude MCP config, the merge materializes a Cursor-specific regular file first; Cursor consumes the same server and schemas as Devin without changing another host's config. OpenCode and Pi retain native adapters.
<!-- /ANCHOR:architecture -->

---

<!-- ANCHOR:phases -->
## 4. IMPLEMENTATION PHASES

### Phase 1: Inspect

- [x] Read the research, preceding closeout, and existing Cursor JSON.

### Phase 2: Merge

- [x] Materialize the Cursor symlink as a regular JSON file while preserving its three inherited server definitions.
- [x] Append one `sk-vision` object under `mcpServers` without rewriting existing values.

### Phase 3: Verify and document

- [x] Parse and assert the config with Node.
- [x] Launch the configured built entry and observe 13 tools.
- [x] Record exact setup and limitations in the five-document suite.
<!-- /ANCHOR:phases -->

---

<!-- ANCHOR:testing -->
## 5. TESTING STRATEGY

| Test Type | Scope | Evidence |
|-----------|-------|----------|
| JSON syntax | Entire `.cursor/mcp.json` | `JSON.parse` succeeds |
| Merge preservation | Original server keys | Parsed key list includes all prior entries |
| Registration shape | `mcpServers.sk-vision` | Command and argument assertion exits 0 |
| Transport smoke | Configured built entry | Official MCP client receives 13 tools |
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:dependencies -->
## 6. DEPENDENCIES

| Dependency | Status | Impact if Blocked |
|------------|--------|-------------------|
| `.cursor/mcp.json` | Available and merged | Cursor cannot discover repository MCP servers |
| Node runtime | Available | Configured process cannot start |
| Built MCP server | Available | sk-vision tools cannot attach |
<!-- /ANCHOR:dependencies -->

---

<!-- ANCHOR:rollback -->
## 7. ROLLBACK PLAN

Remove only `mcpServers.sk-vision` from `.cursor/mcp.json`. The three pre-existing registrations and the shared MCP server remain untouched.
<!-- /ANCHOR:rollback -->
