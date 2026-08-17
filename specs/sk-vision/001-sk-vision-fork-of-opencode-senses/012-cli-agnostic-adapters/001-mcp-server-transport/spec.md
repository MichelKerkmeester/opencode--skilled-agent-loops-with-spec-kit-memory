---
title: "Feature Specification: sk-vision MCP server transport"
description: "Expose all 13 shared sk-vision tools through an additive MCP stdio server without changing the NDJSON runtime core."
trigger_phrases:
  - "sk-vision MCP transport"
  - "sk-vision stdio server"
  - "Cursor Devin vision MCP"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the MCP transport specification metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport/spec.md"
      - ".opencode/skills/sk-vision/vision-runtime/src/mcp/server.ts"
      - ".opencode/skills/sk-vision/vision-runtime/src/mcp/server.test.ts"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-001-mcp-server-transport"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions:
      - "The MCP adapter reuses `skVisionTools` directly, including its descriptions, Zod schemas, and handlers."
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: sk-vision MCP server transport

<!-- SPECKIT_LEVEL: 2 -->

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
| **Predecessor** | None |
| **Successor** | `002-cursor-adapter` |
| **Handoff Criteria** | Built MCP entry lists exactly 13 shared tools and serves `sk_vision_status` over stdio without model weights; package build and all tests pass. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

This Level-2 leaf is the transport foundation for Cursor and Devin. The approved research in `../research/research-report.md` confirms that both hosts are MCP-only, while OpenCode and Pi retain native adapters.

**Scope Boundary:** Add a thin MCP layer inside `vision-runtime/`. Do not modify `src/runtime/client.ts`, `python/runtime.py`, the OpenCode plugin, or the Pi extension.

**Deliverables:** MCP server source, build/bin entry, official SDK dependency, hermetic MCP client test, and package-local launch documentation.
<!-- /ANCHOR:phase-context -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement

Cursor and Devin cannot load the existing OpenCode plugin or Pi extension. The shared runtime is already host-agnostic, but no MCP transport exposes its public tools.

### Purpose

Adapt the existing `skVisionTools` registry into an official MCP stdio server so MCP hosts receive the same 13 schemas, descriptions, handlers, rendering, and RuntimeClient path without duplication.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope

- Reuse `src/opencode/tools.ts` as the shared 13-tool schema and handler source.
- Create `src/mcp/server.ts` with `McpServer` and `StdioServerTransport`.
- Route each MCP call through `PhotonProvider` and the existing `RuntimeClient` NDJSON process.
- Build `dist/mcp-server.js` and expose `sk-vision-mcp` plus `bun run mcp`.
- Test MCP initialize, `tools/list`, and `tools/call` for no-model `sk_vision_status`.
- Document source and built launch commands in `vision-runtime/README.md`.

### Out of Scope

- Cursor or Devin config files; those belong to phases 002 and 003.
- Runtime protocol, model, provider, or context-renderer changes.
- Tool aliases, renamed parameters, or duplicated MCP-only schemas.
- Model downloads or live image inference.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| `.opencode/skills/sk-vision/vision-runtime/src/mcp/server.ts` | Create | MCP registration and stdio entry |
| `.opencode/skills/sk-vision/vision-runtime/src/mcp/server.test.ts` | Create | Hermetic MCP list/status protocol test |
| `.opencode/skills/sk-vision/vision-runtime/scripts/build.ts` | Modify | Emit `dist/mcp-server.js` |
| `.opencode/skills/sk-vision/vision-runtime/package.json` | Modify | SDK dependency, launch script, bin metadata |
| `.opencode/skills/sk-vision/vision-runtime/bun.lock` | Generate | Resolved official SDK dependency |
| `.opencode/skills/sk-vision/vision-runtime/README.md` | Create | MCP launch and architecture reference |
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:requirements -->
## 4. REQUIREMENTS

### P0 - Blockers

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-001 | Expose the complete shared tool surface | MCP `tools/list` returns exactly 13 `sk_vision_*` tools |
| REQ-002 | Reuse existing schemas and handlers | `src/mcp/server.ts` imports and iterates `skVisionTools`; no copied schema table exists |
| REQ-003 | Preserve the shared runtime path | MCP server constructs `RuntimeClient` and `PhotonProvider`; core files remain unmodified |
| REQ-004 | Provide a runnable stdio entry | `bun run mcp` and built `dist/mcp-server.js` are available |
| REQ-005 | Prove no-model operation | MCP client calls `sk_vision_status` and receives text containing `provider: photon` and `loaded: false` |

### P1 - Required

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-P1 | Use the official TypeScript SDK | `package.json` depends on `@modelcontextprotocol/sdk` |
| REQ-P2 | Preserve regression coverage | `bun run build && bun test` reports `9 pass, 0 fail` |
| REQ-P3 | Document host launch contract | `vision-runtime/README.md` gives source and built command/args examples |
<!-- /ANCHOR:requirements -->

---

<!-- ANCHOR:success-criteria -->
## 5. SUCCESS CRITERIA

- [x] Exactly 13 tools are advertised over MCP. Evidence: `src/mcp/server.test.ts` asserts `listed.tools` length 13.
- [x] `sk_vision_status` succeeds over a spawned stdio MCP session without model weights. Evidence: `src/mcp/server.test.ts` asserts `provider: photon` and `loaded: false`.
- [x] Shared schemas and handlers are reused. Evidence: `src/mcp/server.ts` imports `skVisionTools` and registers `definition.args` plus `definition.execute`.
- [x] Runtime core remains unchanged. Evidence: scoped diff contains no changes to `src/runtime/client.ts` or `python/runtime.py`.
- [x] Build and full test suite pass. Evidence: `bun run build && bun test` produced `9 pass, 0 fail` across 3 files.
<!-- /ANCHOR:success-criteria -->

---

<!-- ANCHOR:risks -->
## 6. RISKS & DEPENDENCIES

| Type | Item | Impact | Mitigation |
|------|------|--------|------------|
| Risk | MCP stdout is polluted by logs | Protocol corruption | Server emits no stdout logs; RuntimeClient debug output is stderr-only |
| Risk | Tool schema drift between native and MCP adapters | Host inconsistency | Register `definition.args` from `skVisionTools` directly |
| Risk | Test triggers dependency provisioning or model download | Non-hermetic CI | Test sets an explicit Python interpreter and disables auto-provision; calls status only |
| Dependency | Official MCP TypeScript SDK | Required transport API | Pin package range to the workspace-standard `^1.24.3` |
| Dependency | Existing `RuntimeClient` and `PhotonProvider` | Shared execution path | Import without modifying either component |
<!-- /ANCHOR:risks -->

---

<!-- ANCHOR:questions -->
## 7. OPEN QUESTIONS

### Answered Questions

- **Q:** Should MCP duplicate the tool schemas? **A:** No. It adapts the existing Zod raw shapes from `skVisionTools`.
- **Q:** Should OpenCode and Pi switch to MCP? **A:** No. Their native adapters remain unchanged.

### Open Questions

- None.
<!-- /ANCHOR:questions -->
