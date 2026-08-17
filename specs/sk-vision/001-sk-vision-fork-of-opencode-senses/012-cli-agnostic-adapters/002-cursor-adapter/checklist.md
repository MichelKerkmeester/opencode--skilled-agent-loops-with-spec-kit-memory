---
title: "Verification Checklist: Cursor sk-vision MCP adapter"
description: "Verification Date: 2026-08-16"
trigger_phrases:
  - "Cursor sk-vision MCP adapter checklist"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Cursor adapter checklist evidence."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter/checklist.md"
      - ".cursor/mcp.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-002-cursor-adapter"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
# Verification Checklist: Cursor sk-vision MCP adapter

<!-- SPECKIT_LEVEL: 2 -->
<!-- SPECKIT_TEMPLATE_SOURCE: checklist | v2.2 -->

---

<!-- ANCHOR:protocol -->
## Verification Protocol

| Priority | Handling | Completion Impact |
|----------|----------|-------------------|
| **[P0]** | HARD BLOCKER | Cannot claim done until complete |
| **[P1]** | Required | Must complete or receive approval |
| **[P2]** | Optional | May defer with a reason |
<!-- /ANCHOR:protocol -->

---

<!-- ANCHOR:pre-impl -->
## Pre-Implementation

- [x] CHK-001 [P0] Host contract is documented. Evidence: `../research/research-report.md` confirms Cursor uses `.cursor/mcp.json`.
- [x] CHK-002 [P0] Existing config was read. Evidence: `.cursor/mcp.json` had three pre-existing servers.
- [x] CHK-003 [P1] Shared transport exists. Evidence: `.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.
<!-- /ANCHOR:pre-impl -->

---

<!-- ANCHOR:code-quality -->
## Code Quality

- [x] CHK-010 [P0] Existing entries are preserved. Evidence: `mk-spec-memory`, `mk_skill_advisor`, and `code_mode` remain in `.cursor/mcp.json`.
- [x] CHK-011 [P0] Server key is stable. Evidence: `.cursor/mcp.json` contains `mcpServers.sk-vision`.
- [x] CHK-012 [P0] Command is exact. Evidence: `.cursor/mcp.json` contains `"command": "node"`.
- [x] CHK-013 [P0] Argument is exact and absolute. Evidence: the sole argument is `/Users/michelkerkmeester/MEGA/Development/Code_Environment/Public/.worktrees/012-sk-vision/.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.
<!-- /ANCHOR:code-quality -->

---

<!-- ANCHOR:testing -->
## Testing

- [x] CHK-020 [P0] JSON parses. Evidence: Node `JSON.parse` command exited 0.
- [x] CHK-021 [P0] Registration assertion passes. Evidence: `.cursor/mcp.json` command, argument count, and path-suffix assertions exited 0.
- [x] CHK-022 [P0] Shared process launches under the configured runtime. Evidence: `node dist/mcp-server.js` connected through the official MCP client.
- [x] CHK-023 [P0] Complete tool inventory is exposed. Evidence: `.opencode/skills/sk-vision/vision-runtime/src/mcp/server.test.ts` proves `tools/list` returns 13 named `sk_vision_*` tools.
<!-- /ANCHOR:testing -->

---

<!-- ANCHOR:fix-completeness -->
## Fix Completeness

- [x] CHK-030 [P0] Runtime source is untouched. Evidence: `spec.md` limits this child to Cursor config and spec documents.
<!-- /ANCHOR:fix-completeness -->

---

<!-- ANCHOR:security -->
## Security

- [x] CHK-014 [P1] No environment secrets were added. Evidence: the entry has only `command` and `args` in `.cursor/mcp.json`.
- [x] CHK-015 [P0] Cursor-only configuration does not mutate another host. Evidence: `.cursor/mcp.json` is a regular file and `.claude/mcp.json` has no final diff.
<!-- /ANCHOR:security -->

---

<!-- ANCHOR:docs -->
## Documentation

- [x] CHK-031 [P1] Setup is documented. Evidence: `implementation-summary.md` includes the exact JSON entry.
- [x] CHK-032 [P1] Level-2 suite is complete. Evidence: `spec.md`, `plan.md`, `tasks.md`, `checklist.md`, and `implementation-summary.md` exist.
<!-- /ANCHOR:docs -->

---

<!-- ANCHOR:file-org -->
## File Organization

- [x] CHK-033 [P1] No JSON metadata was authored. Evidence: `checklist.md` is one of the five requested Markdown files.
<!-- /ANCHOR:file-org -->

---

<!-- ANCHOR:summary -->
## Verification Summary

- [x] CHK-040 [P0] Every checklist item cites a concrete artifact. Evidence: all entries in `checklist.md` include backticked files or commands.
- [x] CHK-041 [P0] Cursor registration is ready for host attach. Evidence: `.cursor/mcp.json` plus a successful 13-tool standalone launch.
<!-- /ANCHOR:summary -->
