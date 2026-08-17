---
title: "Tasks: sk-vision MCP server transport"
description: "Executable tasks and evidence for the additive MCP stdio transport."
trigger_phrases:
  - "sk-vision MCP transport tasks"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the MCP transport task evidence."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/001-mcp-server-transport/tasks.md"
      - ".opencode/skills/sk-vision/vision-runtime/src/mcp/server.test.ts"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-001-mcp-server-transport"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: tasks-core | v2.2 -->
# Tasks: sk-vision MCP server transport

<!-- SPECKIT_LEVEL: 2 -->

---

<!-- ANCHOR:notation -->
## Task Notation

| Prefix | Meaning |
|--------|---------|
| `[ ]` | Pending |
| `[x]` | Completed |
| `[P]` | Parallelizable |
| `[B]` | Blocked |

**Task Format:** `T### [P?] Description (file path)`
<!-- /ANCHOR:notation -->

---

<!-- ANCHOR:phase-1 -->
## Phase 1: Setup

- [x] T001 Read and lock the host-model research. Evidence: `../research/research-report.md` confirms MCP-only Cursor/Devin and native OpenCode/Pi.
- [x] T002 Inventory the canonical 13 definitions and NDJSON path. Evidence: `.opencode/skills/sk-vision/vision-runtime/src/opencode/tools.ts`, `.opencode/skills/sk-vision/vision-runtime/src/providers/photon.ts`, and `.opencode/skills/sk-vision/vision-runtime/src/runtime/client.ts`.
- [x] T003 Capture the regression baseline. Evidence: initial `bun test` returned `8 pass, 0 fail` across 2 files.
<!-- /ANCHOR:phase-1 -->

---

<!-- ANCHOR:phase-2 -->
## Phase 2: Implementation

- [x] T004 Add the official MCP SDK and launch metadata. Evidence: `.opencode/skills/sk-vision/vision-runtime/package.json` contains `@modelcontextprotocol/sdk`, `mcp`, and `sk-vision-mcp`.
- [x] T005 Build the MCP stdio adapter over the shared registry. Evidence: `.opencode/skills/sk-vision/vision-runtime/src/mcp/server.ts` imports `skVisionTools`, `PhotonProvider`, and `RuntimeClient`.
- [x] T006 Add a built MCP entry. Evidence: `.opencode/skills/sk-vision/vision-runtime/scripts/build.ts` emits `.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.
- [x] T007 Add the hermetic protocol test. Evidence: `.opencode/skills/sk-vision/vision-runtime/src/mcp/server.test.ts` uses the official MCP client, asserts 13 tools, and calls status with auto-provision disabled.
- [x] T008 Document the package transport. Evidence: `.opencode/skills/sk-vision/vision-runtime/README.md` lists source and built launch commands.
<!-- /ANCHOR:phase-2 -->

---

<!-- ANCHOR:phase-3 -->
## Phase 3: Verification

- [x] T009 Run focused type and MCP checks. Evidence: `bun run typecheck` exit 0; focused MCP test `1 pass, 0 fail`.
- [x] T010 Run the authoritative package gate. Evidence: `bun run build && bun test` exit 0; `9 pass, 0 fail`, 36 assertions, 3 test files.
- [x] T011 Confirm core and unrelated paths are untouched. Evidence: scoped diff includes no change to `.opencode/skills/sk-vision/vision-runtime/src/runtime/client.ts`, `.opencode/skills/sk-vision/vision-runtime/python/runtime.py`, OpenCode/Pi adapters, `context/`, or deep-loop runtime.
- [x] T012 Complete the Level-2 evidence suite. Evidence: `spec.md`, `plan.md`, `tasks.md`, `checklist.md`, and `implementation-summary.md` exist in this child and cite concrete artifacts.
<!-- /ANCHOR:phase-3 -->

---

<!-- ANCHOR:completion -->
## Completion Criteria

- [x] All tasks marked `[x]`. Evidence: T001-T012 above.
- [x] No `[B]` tasks remain. Evidence: zero blocked task entries.
- [x] Authoritative verification passed. Evidence: `bun run build && bun test` returned `9 pass, 0 fail`.
<!-- /ANCHOR:completion -->

---

<!-- ANCHOR:cross-refs -->
## Cross-References

- **Specification:** `spec.md`
- **Plan:** `plan.md`
- **Verification:** `checklist.md`
- **Closeout:** `implementation-summary.md`
<!-- /ANCHOR:cross-refs -->
