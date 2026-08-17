---
title: "Implementation Summary: Cursor sk-vision MCP adapter"
description: "Closeout evidence for the merged Cursor registration of the 13-tool sk-vision MCP server."
trigger_phrases:
  - "Cursor sk-vision MCP adapter summary"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Cursor adapter closeout metadata."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter/implementation-summary.md"
      - ".cursor/mcp.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-002-cursor-adapter"
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
| **Spec Folder** | 002-cursor-adapter |
| **Completed** | 2026-08-16 |
| **Level** | 2 |
| **Status** | Complete |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:what-built -->
## What Was Built

The repository's existing `.cursor/mcp.json` now includes a fourth `mcpServers` entry named `sk-vision`. The merge preserved `mk-spec-memory`, `mk_skill_advisor`, and `code_mode` exactly as they were.

The original Cursor path was a symlink to the shared Claude MCP config. It was materialized as a Cursor-specific regular JSON file before adding `sk-vision`, so the requested adapter does not alter `.claude/mcp.json` or other hosts that consume the shared symlink target.

The added entry is:

```json
"sk-vision": {
  "command": "node",
  "args": [
    "/Users/michelkerkmeester/MEGA/Development/Code_Environment/Public/.worktrees/012-sk-vision/.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js"
  ]
}
```
<!-- /ANCHOR:what-built -->

---

<!-- ANCHOR:how-delivered -->
## How It Was Delivered

Cursor reads the repository-local file, spawns the built MCP server over stdio with Node, and discovers the 13 canonical `sk_vision_*` tools through MCP `tools/list`. No Cursor-specific runtime or schema copy was introduced.

After opening this checkout in Cursor, reload the MCP configuration or restart Cursor. The `sk-vision` server should appear in Cursor's MCP settings and expose the same tool names proven by the standalone transport check.
<!-- /ANCHOR:how-delivered -->

---

<!-- ANCHOR:decisions -->
## Key Decisions

| Decision | Why |
|----------|-----|
| Merge instead of replace | Preserves three existing repository MCP services |
| Materialize the Cursor symlink | Isolates the Cursor-only registration from the shared Claude MCP config |
| Use server key `sk-vision` | Gives Cursor a stable, human-readable registration name |
| Use the required absolute argument | Makes the checkout-specific launch target unambiguous |
| Reuse the built stdio server | Avoids a Cursor-only adapter and keeps tool schemas identical |
<!-- /ANCHOR:decisions -->

---

<!-- ANCHOR:verification -->
## Verification

| Check | Result |
|-------|--------|
| Cursor JSON parse | exit 0 |
| Existing key preservation | `mk-spec-memory`, `mk_skill_advisor`, and `code_mode` present |
| Host isolation | `.claude/mcp.json` has no final diff |
| sk-vision shape assertion | command `node`; one absolute argument; exit 0 |
| Built process launch | official MCP client connected with Node |
| MCP tool list | exactly 13 tools |
| Runtime/core scope | no runtime source changed in this child |
<!-- /ANCHOR:verification -->

---

<!-- ANCHOR:limitations -->
## KNOWN LIMITATIONS

- The absolute argument is specific to this worktree path and must change if the checkout moves.
- Host-native Cursor attachment was not automated from this terminal session; the standalone launch proves the exact configured process and protocol surface.
- MCP exposes explicit tool calls but does not add native attachment-input hooks.
- Spec-kit validation was intentionally not run; the conductor owns validation and JSON metadata generation on main.
<!-- /ANCHOR:limitations -->
