---
title: "Tasks: Devin sk-vision MCP adapter"
description: "Completed configuration, namespace documentation, and verification tasks for Devin MCP attachment."
trigger_phrases:
  - "Devin sk-vision MCP adapter tasks"
importance_tier: "important"
contextType: "implementation"
_memory:
  continuity:
    packet_pointer: "sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/003-devin-adapter"
    last_updated_at: "2026-08-16T21:15:43.000Z"
    last_updated_by: "sol"
    recent_action: "Conformed the Devin adapter task evidence."
    next_safe_action: "Conductor validates the 012 subtree on main."
    blockers: []
    key_files:
      - "specs/sk-vision/001-sk-vision-fork-of-opencode-senses/012-cli-agnostic-adapters/003-devin-adapter/tasks.md"
      - ".devin/mcp_config.json"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "sk-vision-012-003-devin-adapter"
      parent_session_id: null
    completion_pct: 100
    open_questions: []
    answered_questions: []
---
<!-- SPECKIT_TEMPLATE_SOURCE: tasks-core | v2.2 -->
# Tasks: Devin sk-vision MCP adapter

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

- [x] T001 Confirm Devin's MCP-only host model and namespace. Evidence: `../research/research-report.md` lines 19 and 34.
- [x] T002 Inspect `.devin/` for an existing project config. Evidence: no `mcp_config.json` existed.
- [x] T003 Confirm the common server artifact. Evidence: `../001-mcp-server-transport/implementation-summary.md` and `.opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js`.
<!-- /ANCHOR:phase-1 -->

---

<!-- ANCHOR:phase-2 -->
## Phase 2: Implementation

- [x] T004 Create `.devin/mcp_config.json`. Evidence: the new file has a root `mcpServers` object.
- [x] T005 Add `mcpServers.sk-vision`. Evidence: command `node` and one absolute built-server argument.
- [x] T006 Document Devin namespacing. Evidence: `implementation-summary.md` and playbook references use `mcp__sk-vision__<tool>`.
<!-- /ANCHOR:phase-2 -->

---

<!-- ANCHOR:phase-3 -->
## Phase 3: Verification

- [x] T007 Parse and assert the config. Evidence: Node parsed `.devin/mcp_config.json`, exited 0, and printed `servers: ["sk-vision"]`.
- [x] T008 Smoke-test the configured process. Evidence: `node .opencode/skills/sk-vision/vision-runtime/dist/mcp-server.js` listed 13 tools through the official MCP client.
- [x] T009 Complete the Level-2 suite. Evidence: `spec.md`, `plan.md`, `tasks.md`, `checklist.md`, and `implementation-summary.md`.
<!-- /ANCHOR:phase-3 -->

---

<!-- ANCHOR:completion -->
## Completion Criteria

- [x] All tasks are complete. Evidence: T001-T009 are `[x]`.
- [x] No blocked tasks remain. Evidence: no `[B]` entries.
- [x] Devin's repository config is valid and launchable. Evidence: `.devin/mcp_config.json` assertion and 13-tool MCP smoke check both exited 0.
<!-- /ANCHOR:completion -->

---

<!-- ANCHOR:cross-refs -->
## Cross-References

- **Specification:** `spec.md`
- **Plan:** `plan.md`
- **Verification:** `checklist.md`
- **Closeout:** `implementation-summary.md`
<!-- /ANCHOR:cross-refs -->
