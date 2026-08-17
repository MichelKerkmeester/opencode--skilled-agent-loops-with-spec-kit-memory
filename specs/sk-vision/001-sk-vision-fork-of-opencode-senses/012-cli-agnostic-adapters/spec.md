---
title: "sk-vision CLI-agnostic adapters"
description: "Phase parent for exposing the shared sk-vision runtime to MCP-only coding agents while preserving native OpenCode and Pi adapters."
trigger_phrases:
  - "sk-vision MCP server"
  - "sk-vision Cursor adapter"
  - "sk-vision Devin adapter"
  - "CLI-agnostic vision adapters"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters"
    last_updated_at: "2026-08-17T00:03:36.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the phase-parent specification metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/spec.md"
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport/spec.md"
      - ".opencode/skills/sk-vision/vision-runtime/src/mcp/server.ts"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-cli-agnostic-adapters"
      parent_session_id: null
    completion_pct: 25
    open_questions: []
    answered_questions:
      - "Cursor and Devin use the same MCP stdio transport; OpenCode and Pi retain native adapters."
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->

<!-- SPECKIT_LEVEL: 2 -->
<!-- CONTENT DISCIPLINE: PHASE PARENT
  Detailed plans, tasks, checklists, decisions, and implementation summaries live in child phase folders.
-->

# Feature Specification: sk-vision CLI-agnostic adapters

<!-- ANCHOR:metadata -->
## 1. METADATA

| Field | Value |
|-------|-------|
| **Level** | Phase parent |
| **Priority** | P0 |
| **Status** | In Progress |
| **Created** | 2026-08-16 |
| **Branch** | `worktrees/012-sk-vision` |
| **Parent Spec** | `../spec.md` |
| **Parent Packet** | `001-sk-vision-fork-of-opencode-senses` |
| **Predecessor** | `011-live-validation` |
| **Successor** | Parent completion |
| **Handoff Criteria** | One shared MCP stdio server exposes all 13 tools; Cursor and Devin launch it through their MCP configuration; catalog and playbook coverage describe all four hosts. |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement

The shared `RuntimeClient`, providers, and context renderer are host-agnostic, but only OpenCode and Pi can load native in-process adapters. Cursor and Devin expose external tools through MCP and therefore cannot reach the 13 sk-vision tools without a separate transport.

### Purpose

Add one MCP stdio transport over the existing runtime, then configure Cursor and Devin to launch it. Keep the native OpenCode and Pi paths unchanged and document the resulting four-host surface.

> **Phase-parent note:** This `spec.md` is the only authored Markdown document at this level. Detailed implementation and verification evidence belongs in the child phases listed below.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope

- An additive MCP stdio server in `.opencode/skills/sk-vision/vision-runtime/`.
- MCP registration of the existing 13 `sk_vision_*` definitions without duplicated schemas or handlers.
- Cursor and Devin MCP configuration in separate follow-on children.
- Feature-catalog and manual-testing-playbook coverage for the MCP, Cursor, and Devin surfaces.
- Native OpenCode and Pi adapters remaining the preferred in-process paths.

### Out of Scope

- Replacing the OpenCode plugin or Pi extension with MCP.
- Modifying the Python NDJSON runtime or `RuntimeClient` protocol.
- Adding an in-process Cursor or Devin plugin API that those hosts do not provide.
- Changing model behavior, tool semantics, or public tool names.

### Files to Change

| File Path | Change Type | Phase | Description |
|-----------|-------------|-------|-------------|
| `001-mcp-server-transport/` | Create | 001 | Level-2 implementation and evidence suite |
| `.opencode/skills/sk-vision/vision-runtime/src/mcp/` | Create | 001 | MCP server and hermetic protocol test |
| `.opencode/skills/sk-vision/vision-runtime/scripts/build.ts` | Modify | 001 | Build `dist/mcp-server.js` |
| `.opencode/skills/sk-vision/vision-runtime/package.json` | Modify | 001 | MCP SDK dependency, script, and bin entry |
| `.cursor/mcp.json` | Planned | 002 | Cursor launch registration |
| `.devin/mcp_config.json` | Planned | 003 | Devin launch registration |
| `.opencode/skills/sk-vision/{feature-catalog,manual-testing-playbook}/` | Planned | 004 | Multi-host documentation coverage |
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:phase-map -->
## PHASE DOCUMENTATION MAP

> Each child is independently executable and owns its detailed plan, task list, checklist, and closeout evidence. This parent remains the lean trio `spec.md`, `description.json`, and `graph-metadata.json`; the conductor generates the JSON metadata files.

**Directory status:** `001-mcp-server-transport` is Complete. Children `002` through `004` are Planned and are not implemented by phase 001.

| Phase | Folder | Title / Focus | Level | Status |
|-------|--------|---------------|-------|--------|
| 1 | `001-mcp-server-transport/` | Shared 13-tool MCP stdio wrapper and hermetic protocol test | 2 | Complete |
| 2 | `002-cursor-adapter/` | Cursor `.cursor/mcp.json` launch configuration and proof | 2 | Planned |
| 3 | `003-devin-adapter/` | Devin `.devin/mcp_config.json` launch configuration and proof | 2 | Planned |
| 4 | `004-catalog-and-playbook/` | Host-adapter catalog and per-CLI testing scenarios | 2 | Planned |

### 001-mcp-server-transport (Complete)

Add the official MCP TypeScript SDK, adapt the existing `skVisionTools` registry into MCP registration, build a runnable stdio entry, and prove `tools/list` plus `sk_vision_status` without model weights. Evidence lives in `001-mcp-server-transport/implementation-summary.md`.

### 002-cursor-adapter (Planned)

Register the built server in `.cursor/mcp.json`, document an absolute-path-safe launch contract, and prove Cursor discovers all 13 tools.

### 003-devin-adapter (Planned)

Register the same built server in `.devin/mcp_config.json` and prove Devin discovers the namespaced tool surface.

### 004-catalog-and-playbook (Planned)

Add MCP, Cursor, and Devin host-adapter entries to the feature catalog and deterministic standalone/per-host scenarios to the manual testing playbook.

### Phase Transition Rules

- Phase 002 starts only after `dist/mcp-server.js`, the 13-tool list, and no-model status call are proven.
- Phases 002 and 003 consume the same MCP command; neither may fork schemas or runtime logic.
- Phase 004 documents behavior already shipped by phases 001-003.
- OpenCode and Pi native adapters remain untouched throughout this packet.

### Phase Handoff Criteria

| From | To | Criteria | Verification |
|------|----|----------|--------------|
| 001 | 002 | MCP server builds, lists 13 tools, and returns status without loading weights | `bun run build && bun test` reports `9 pass, 0 fail` |
| 002 | 003 | Cursor configuration launches the shared server and discovers all tools | Cursor MCP tool inventory evidence |
| 003 | 004 | Devin configuration launches the same server and discovers all tools | Devin MCP tool inventory evidence |
| 004 | Parent completion | Catalog and playbook cover MCP, Cursor, and Devin | Package validators and recorded scenarios |
<!-- /ANCHOR:phase-map -->

---

<!-- ANCHOR:questions -->
## 4. OPEN QUESTIONS

- None. The research report locks MCP as the universal fallback and native adapters as the OpenCode/Pi path.
<!-- /ANCHOR:questions -->

---

## RELATED DOCUMENTS

- **Research basis**: `research/research-report.md`
- **Active child evidence**: `001-mcp-server-transport/implementation-summary.md`
- **Parent packet**: `../spec.md`
