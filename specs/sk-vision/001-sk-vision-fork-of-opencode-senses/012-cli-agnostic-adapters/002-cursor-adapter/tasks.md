---
title: "Tasks: Cursor sk-vision MCP adapter"
description: "Completed merge, verification, and documentation tasks for Cursor MCP attachment."
trigger_phrases:
  - "Cursor sk-vision MCP adapter tasks"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Cursor adapter task evidence."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/002-cursor-adapter/tasks.md"
      - ".cursor/mcp.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-002-cursor-adapter"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: tasks-core | v2.2 -->
# Tasks: Cursor sk-vision MCP adapter

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

- [x] T001 Confirm Cursor's MCP-only host model. Evidence: `../research/research-report.md` lines 18 and 33.
- [x] T002 Read the existing Cursor config before editing. Evidence: `.cursor/mcp.json` contained `mk-spec-memory`, `mk_skill_advisor`, and `code_mode`.
- [x] T003 Confirm the preceding transport artifact. Evidence: `../001-mcp-server-transport/implementation-summary.md` and `.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.
<!-- /ANCHOR:phase-1 -->

---

<!-- ANCHOR:phase-2 -->
## Phase 2: Implementation

- [x] T004 Merge `mcpServers.sk-vision`. Evidence: `.cursor/mcp.json`.
- [x] T005 Set the approved stdio launch contract. Evidence: command `node` and one absolute `dist/mcp-server.js` argument.
- [x] T006 Preserve existing entries. Evidence: parsed `.cursor/mcp.json` key list contains four servers, including all three original keys.
- [x] T006A Isolate the Cursor-only entry from the original shared symlink target. Evidence: `.cursor/mcp.json` is a regular file and `.claude/mcp.json` has no final diff.
<!-- /ANCHOR:phase-2 -->

---

<!-- ANCHOR:phase-3 -->
## Phase 3: Verification

- [x] T007 Parse and assert the JSON. Evidence: Node parsed `.cursor/mcp.json`, exited 0, and printed the four server keys plus the exact `skVision` object.
- [x] T008 Smoke-test the configured process. Evidence: `node .opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js` listed 13 tool names through the official MCP client.
- [x] T009 Complete the Level-2 suite. Evidence: `spec.md`, `plan.md`, `tasks.md`, `checklist.md`, and `implementation-summary.md`.
<!-- /ANCHOR:phase-3 -->

---

<!-- ANCHOR:completion -->
## Completion Criteria

- [x] All tasks are complete. Evidence: T001-T009 are `[x]`.
- [x] No blocked tasks remain. Evidence: no `[B]` entries.
- [x] Cursor config remains a valid merged JSON document. Evidence: Node parse/assert of `.cursor/mcp.json` exited 0.
<!-- /ANCHOR:completion -->

---

<!-- ANCHOR:cross-refs -->
## Cross-References

- **Specification:** `spec.md`
- **Plan:** `plan.md`
- **Verification:** `checklist.md`
- **Closeout:** `implementation-summary.md`
<!-- /ANCHOR:cross-refs -->
