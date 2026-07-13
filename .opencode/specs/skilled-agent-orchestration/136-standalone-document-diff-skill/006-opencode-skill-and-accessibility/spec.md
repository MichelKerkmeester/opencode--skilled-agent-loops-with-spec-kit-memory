---
title: "Feature Specification: OpenCode skill wrapper and accessibility refinement"
description: "Package the portable document diff engine as a standalone OpenCode skill with safe before-edit capture, automatic after-edit review, explicit-pair fallback, and fully accessible report workflows."
trigger_phrases:
  - "OpenCode document diff skill"
  - "AI edit before after review"
  - "accessible document diff report"
  - "automatic document snapshot"
importance_tier: "important"
contextType: "implementation"
status: "draft"
_memory:
  continuity:
    packet_pointer: "skilled-agent-orchestration/136-standalone-document-diff-skill/006-opencode-skill-and-accessibility"
    last_updated_at: "2026-07-13T18:30:00Z"
    last_updated_by: "codex"
    recent_action: "Scaffolded the OpenCode skill and accessibility phase"
    next_safe_action: "Wait for the portable API and CLI to stabilize, then run skill creation intake"
    blockers:
      - "Phases 002 through 005 must expose a stable portable contract"
    key_files:
      - "spec.md"
      - "plan.md"
      - "tasks.md"
    session_dedup:
      fingerprint: "sha256:0000000000000000000000000000000000000000000000000000000000000000"
      session_id: "document-diff-phase-006-scaffold"
      parent_session_id: null
    completion_pct: 0
    open_questions:
      - "What final local skill slug and installation path should be used?"
    answered_questions:
      - "The skill is an orchestration wrapper and must not duplicate portable core logic."
      - "Automatic capture is the default; explicit before and after files remain the fallback."
---
<!-- SPECKIT_TEMPLATE_SOURCE: spec-core | v2.2 -->
# Feature Specification: OpenCode skill wrapper and accessibility refinement

<!-- SPECKIT_LEVEL: 1 -->

---

<!-- ANCHOR:metadata -->
## 1. METADATA

| Field | Value |
|-------|-------|
| **Level** | 1 scaffold; re-evaluate at implementation intake |
| **Priority** | P1 |
| **Status** | Draft; gated by stable phases 002 through 005 |
| **Created** | 2026-07-13 |
| **Branch** | `main` |
| **Parent Spec** | `../spec.md` |
| **Phase** | 6 of 7 |
| **Predecessor** | `../005-pdf-cli-and-cross-platform-state/spec.md` |
| **Successor** | `../007-optional-ocr-adapter/spec.md` |
| **Handoff Criteria** | OpenCode users receive a safe automatic before and after review while the same portable engine remains usable outside OpenCode |
<!-- /ANCHOR:metadata -->

---

<!-- ANCHOR:phase-context -->
## Phase Context

This phase turns the verified portable core into the user-facing OpenCode skill. It also closes accessibility gaps across the unified, side-by-side, and fidelity report views before the required v1 surface is declared ready.

**Scope Boundary**: Add only OpenCode-specific orchestration, skill instructions, command routing, capability guidance, and accessibility refinements. All extraction, diffing, snapshots, cleanup, and rendering continue to live in the portable package.

**Dependencies**:

- Stable public API and CLI from phases 002 through 005.
- Passing phase 003 security, accessibility, dependency, license, and performance gates.
- The sk-doc create-skill workflow and its generated skill validation.

**Deliverables**:

- Standalone OpenCode skill package with precise triggers, bounded workflow, and local-only defaults.
- Automatic before-edit capture and after-edit comparison orchestration.
- Explicit before and after fallback when no valid snapshot exists.
- Status, cleanup, and capability guidance that delegates to the portable CLI.
- Keyboard, screen-reader, contrast, RTL, CJK, zoom, and zero-JavaScript report verification.
<!-- /ANCHOR:phase-context -->

---

<!-- ANCHOR:problem -->
## 2. PROBLEM & PURPOSE

### Problem Statement

A portable CLI alone does not capture the moment before an AI edit or guide users through review inside OpenCode. A thin wrapper is needed, but it must not fork product logic or weaken local-only, security, and accessibility guarantees.

### Purpose

Deliver a standalone OpenCode skill that automatically preserves the before state, generates an accessible local review after edits, and falls back cleanly to explicit file pairs.
<!-- /ANCHOR:problem -->

---

<!-- ANCHOR:scope -->
## 3. SCOPE

### In Scope

- Skill creation through the repository sk-doc create-skill workflow.
- Trigger phrases and workflow instructions for document edits and explicit review requests.
- Before-edit capture before any authorized mutation and after-edit comparison after a successful write.
- Explicit pair fallback when automatic state is missing, invalid, locked, or unsupported.
- Wrapper commands or documented routes for capture, compare, status, cleanup, and capability inspection.
- Workspace-safe path validation, capability-tier messages, local-only defaults, and actionable failures.
- Accessibility refinement for summaries, navigation, keyboard control, focus, ARIA, contrast, zoom, RTL, CJK, and no-script use.
- Tests proving the wrapper delegates to the portable API or CLI without reimplementing algorithms.

### Out of Scope

- New format parsers, diff algorithms, snapshot storage, or report rendering inside the skill.
- Remote uploads, telemetry, hosted reports, or runtime asset downloads.
- OCR implementation; phase 007 owns its conditional adapter.

### Files to Change

| File Path | Change Type | Description |
|-----------|-------------|-------------|
| Future standalone OpenCode skill folder | Create | Skill instructions, references, scripts, and validation artifacts selected through create-skill intake |
| Future portable package integration tests | Modify | Verify wrapper delegation, error mapping, and automatic capture flow |
| Report accessibility fixtures and tests | Modify | Refine and certify every report view and fallback |
<!-- /ANCHOR:scope -->

---

<!-- ANCHOR:requirements -->
## 4. REQUIREMENTS

### P0 - Blockers (MUST complete)

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-001 | Capture a valid before state before an AI document edit. | Integration tests prove capture happens before the first write and failures stop or route to the documented fallback without mutating the source. |
| REQ-002 | Generate the after-edit comparison from the portable engine. | The skill delegates to the stable API or CLI and produces the same deterministic report as direct portable use. |
| REQ-003 | Support explicit before and after pairs as a complete fallback. | A user can generate the report without stored state or OpenCode-specific metadata. |
| REQ-004 | Keep all processing local by default. | Tests and documentation show zero required network access, uploads, telemetry, or runtime downloads. |
| REQ-005 | Meet the report accessibility contract across all views. | Automated and manual keyboard, screen-reader, contrast, zoom, RTL, CJK, and no-script checks pass. |

### P1 - Required (complete OR user-approved deferral)

| ID | Requirement | Acceptance Criteria |
|----|-------------|---------------------|
| REQ-006 | Explain format capability and fidelity tiers in simple terms. | Users see supported, limited, conditional, or unsupported status before a misleading comparison can occur. |
| REQ-007 | Package and validate the skill without core duplication. | Skill validation passes and source inspection shows orchestration only, with algorithms owned by the portable package. |
| REQ-008 | Expose safe status and cleanup guidance. | Wrapper routes preserve dry-run behavior, path controls, and the portable CLI exit contract. |
<!-- /ANCHOR:requirements -->

---

<!-- ANCHOR:success-criteria -->
## 5. SUCCESS CRITERIA

- **SC-001**: Supported AI edit flows capture before the write and open or report the generated local review afterward.
- **SC-002**: Missing automatic state routes to an explicit pair without hidden data loss or a false success.
- **SC-003**: Direct CLI and skill-driven comparison produce byte-equivalent canonical and report output for the same inputs.
- **SC-004**: All required accessibility and zero-network checks pass for unified, side-by-side, and fidelity views.
- **SC-005**: Skill creation and repository validation pass with no duplicate diff or parser implementation.
<!-- /ANCHOR:success-criteria -->

---

<!-- ANCHOR:risks -->
## 6. RISKS & DEPENDENCIES

| Type | Item | Impact | Mitigation |
|------|------|--------|------------|
| Dependency | Stable portable contract | Wrapper churn or duplicated logic | Freeze API, CLI, exit codes, and capability diagnostics before integration |
| Risk | Capture occurs after mutation | The before state is irrecoverable | Make pre-write capture an ordering invariant and test interrupted edits |
| Risk | Skill grows into a second product core | Direct CLI and OpenCode behavior diverge | Limit the wrapper to orchestration and contract mapping |
| Risk | Visual report works but assistive review fails | Required users cannot inspect changes | Treat manual accessibility checks as release blockers |
<!-- /ANCHOR:risks -->

---

<!-- ANCHOR:questions -->
## 7. OPEN QUESTIONS

- Which final skill slug and installation boundary best match existing OpenCode skill conventions at implementation time?
- Which OpenCode edit surfaces expose a reliable pre-write hook, and which must use an explicit capture step?
<!-- /ANCHOR:questions -->

---

## RELATED DOCUMENTS

- Research synthesis: `../001-research-and-requirements/research/research.md`
- Quality gates: `../003-validation-security-and-quality-gates/spec.md`
- Portable CLI and state: `../005-pdf-cli-and-cross-platform-state/spec.md`
- Conditional OCR: `../007-optional-ocr-adapter/spec.md`
