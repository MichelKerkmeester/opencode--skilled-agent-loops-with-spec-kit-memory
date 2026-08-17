---
title: "Implementation Summary: sk-vision MCP server transport"
description: "Closeout evidence for the additive 13-tool MCP stdio transport."
trigger_phrases:
  - "sk-vision MCP transport summary"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the MCP transport closeout metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport/implementation-summary.md"
      - ".opencode/skills/sk-vision/vision-runtime/src/mcp/server.ts"
      - ".opencode/skills/sk-vision/vision-runtime/src/mcp/server.test.ts"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-001-mcp-server-transport"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: impl-summary-core | v2.2 -->
# Implementation Summary

<!-- SPECKIT_LEVEL: 2 -->

---

<!-- ANCHOR:metadata -->
## Metadata

| Field | Value |
|-------|-------|
| **Spec Folder** | 001-mcp-server-transport |
| **Completed** | 2026-08-16 |
| **Level** | 2 |
| **Status** | Complete |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:what-built -->
## What Was Built

An official MCP stdio server now exposes the existing 13 `sk_vision_*` tools to MCP-only hosts. `src/mcp/server.ts` constructs the same `RuntimeClient` and `PhotonProvider` used by native adapters, iterates `skVisionTools`, passes each existing Zod raw shape to MCP registration, and delegates calls to each existing handler.

The package now provides:

- Source launch: `bun run mcp`.
- Built launch: `bun dist/mcp-server.js`.
- Bin metadata: `sk-vision-mcp` -> `dist/mcp-server.js`.
- Build output: `dist/plugin.js`, `dist/mcp-server.js`, and `dist/python/runtime.py`.
- Protocol integration test: `src/mcp/server.test.ts`.
<!-- /ANCHOR:what-built -->

---

<!-- ANCHOR:how-delivered -->
## How It Was Delivered

The implementation imports the existing OpenCode-facing tool definitions as a shared registry rather than creating a new MCP schema table. The adapter converts each existing tool result into MCP text content. MCP session close invokes `RuntimeClient.close()`, preserving subprocess cleanup. The test launches the source entry through `StdioClientTransport`, lists tools, and calls `sk_vision_status` with provisioning disabled.
<!-- /ANCHOR:how-delivered -->

---

<!-- ANCHOR:decisions -->
## Key Decisions

| Decision | Why |
|----------|-----|
| Reuse `skVisionTools` directly | Keeps names, descriptions, Zod schemas, defaults, handlers, and error text identical across native and MCP transports |
| Keep `PhotonProvider` between MCP and RuntimeClient | Preserves path/URL resolution and context rendering instead of bypassing shared behavior |
| Build a separate `dist/mcp-server.js` | Gives Cursor and Devin a stable stdio launch target without changing `dist/plugin.js` |
| Test `status` only | Proves the complete MCP and NDJSON path without model weights, downloads, or GPU requirements |
| Retain native OpenCode/Pi adapters | They provide lower-overhead host integration and attachment hooks unavailable through the universal fallback |
<!-- /ANCHOR:decisions -->

---

<!-- ANCHOR:verification -->
## Verification

| Check | Result |
|-------|--------|
| Baseline `bun test` | `8 pass, 0 fail`, 27 assertions, 2 files |
| Focused MCP test | `1 pass, 0 fail`, 9 assertions |
| `bun run typecheck` | exit 0 |
| `bun run build` | exit 0; emitted `dist/plugin.js + dist/mcp-server.js + dist/python/runtime.py` |
| Final `bun test` | `9 pass, 0 fail`, 36 assertions, 3 files |
| MCP tool list | exactly 13 tools; includes `sk_vision_status` |
| MCP status call | success text includes `provider: photon` and `loaded: false` |
| Core preservation | no changes to `src/runtime/client.ts` or `python/runtime.py` |
<!-- /ANCHOR:verification -->

---

<!-- ANCHOR:limitations -->
## KNOWN LIMITATIONS

- This phase does not add `.cursor/mcp.json` or `.devin/mcp_config.json`; phases 002 and 003 own those host registrations.
- MCP provides explicit tool calls but not the native OpenCode/Pi attachment-input hooks.
- Model-backed tools retain their existing first-run dependency and weight requirements; only status is proven without model weights here.
- Spec-kit validators were intentionally not run in this bare worktree. The conductor owns JSON metadata generation and validation on main.
- Nothing was committed or pushed.
<!-- /ANCHOR:limitations -->
