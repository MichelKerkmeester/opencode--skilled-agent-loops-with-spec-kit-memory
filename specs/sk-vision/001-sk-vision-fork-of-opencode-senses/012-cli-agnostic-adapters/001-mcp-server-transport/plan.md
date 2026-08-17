---
title: "Implementation Plan: sk-vision MCP server transport"
description: "Adapt the shared sk-vision tool registry to an official MCP stdio server and verify it hermetically."
trigger_phrases:
  - "sk-vision MCP transport plan"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the MCP transport plan metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport/plan.md"
      - ".opencode/skills/sk-vision/vision-runtime/src/mcp/server.ts"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-001-mcp-server-transport"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: plan-core | v2.2 -->
# Implementation Plan: sk-vision MCP server transport

<!-- SPECKIT_LEVEL: 2 -->

---

<!-- ANCHOR:summary -->
## 1. SUMMARY

### Technical Context

| Aspect | Value |
|--------|-------|
| **Language/Stack** | TypeScript, Bun, Python NDJSON child process |
| **Framework** | Official MCP TypeScript SDK |
| **Storage** | Existing user cache only; no new persistence |
| **Testing** | Bun test plus an official MCP stdio client |

### Overview

Construct the existing RuntimeClient and PhotonProvider, obtain the canonical `skVisionTools` object, and register each definition with `McpServer`. Convert each existing string result into MCP text content and leave all runtime/provider/tool behavior unchanged.
<!-- /ANCHOR:summary -->

---

<!-- ANCHOR:quality-gates -->
## 2. QUALITY GATES

### Definition of Ready

- [x] Research confirms Cursor and Devin are MCP-only. Evidence: `../research/research-report.md` sections 2-4.
- [x] Canonical tool source identified. Evidence: `src/opencode/tools.ts` exports `skVisionTools` with 13 definitions.
- [x] Runtime execution path identified. Evidence: `PhotonProvider` delegates to `RuntimeClient.request`.

### Definition of Done

- [x] All requirements met. Evidence: REQ-001 through REQ-P3 map to checks in `checklist.md`.
- [x] Package build and full tests pass. Evidence: `bun run build && bun test` reports `9 pass, 0 fail`.
- [x] Launch documentation exists. Evidence: `vision-runtime/README.md`.
<!-- /ANCHOR:quality-gates -->

---

<!-- ANCHOR:architecture -->
## 3. ARCHITECTURE

### Pattern

Thin transport adapter over the existing tool-definition registry.

### Key Components

- **MCP server:** owns MCP initialization, tool registration, and text-result framing.
- **Shared tool registry:** remains the source for tool names, descriptions, Zod schemas, and handlers.
- **PhotonProvider:** preserves path/URL handling and provider semantics.
- **RuntimeClient:** preserves the NDJSON subprocess protocol and lifecycle.

### Data Flow

MCP host -> `StdioServerTransport` -> shared tool definition -> `PhotonProvider` -> `RuntimeClient` -> `python/runtime.py` -> rendered text -> MCP text content.
<!-- /ANCHOR:architecture -->

---

<!-- ANCHOR:phases -->
## 4. IMPLEMENTATION PHASES

### Phase 1: Dependency and transport

- [x] Add the official MCP SDK and package launch metadata.
- [x] Register every shared tool definition with the MCP server.

### Phase 2: Build and protocol test

- [x] Emit `dist/mcp-server.js` alongside `dist/plugin.js`.
- [x] Spawn the server with the official MCP client and assert 13 tools plus status.

### Phase 3: Documentation and closeout

- [x] Add package-local MCP launch documentation.
- [x] Run typecheck, build, full tests, and scoped-diff review.
<!-- /ANCHOR:phases -->

---

<!-- ANCHOR:testing -->
## 5. TESTING STRATEGY

| Test Type | Scope | Tools |
|-----------|-------|-------|
| Type safety | MCP adapter and test | `bun run typecheck` |
| Protocol integration | Initialize, list tools, call status | Official `Client` + `StdioClientTransport` |
| Regression | Existing provider/runtime tests plus MCP test | `bun test` |
| Build | Plugin, MCP server, copied Python runtime | `bun run build` |

The MCP test explicitly selects a Python interpreter, sets `SK_VISION_DISABLE_AUTO_PROVISION=1`, and calls only `status`, so it does not download weights or load the model.
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:dependencies -->
## 6. DEPENDENCIES

| Dependency | Type | Status | Impact if Blocked |
|------------|------|--------|-------------------|
| `@modelcontextprotocol/sdk` | Runtime | Installed | No standards-compliant MCP transport |
| `skVisionTools` | Internal | Available | No canonical schemas or handlers |
| `PhotonProvider` / `RuntimeClient` | Internal | Available | No shared NDJSON execution path |
| Python runtime status handler | Internal | Available | No hermetic no-model call proof |
<!-- /ANCHOR:dependencies -->

---

<!-- ANCHOR:rollback -->
## 7. ROLLBACK PLAN

- **Trigger:** MCP transport causes package build or existing tests to fail.
- **Procedure:** Remove `src/mcp/`, the MCP build entry, package MCP dependency/script/bin fields, package README, and generated `dist/mcp-server.js`. Native OpenCode/Pi behavior remains unaffected because no existing runtime or adapter file was changed.
<!-- /ANCHOR:rollback -->
