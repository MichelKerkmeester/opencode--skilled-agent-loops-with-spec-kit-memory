---
title: Pi Remote DQI Baseline
description: Last measured Document Quality Index for every doc this sk-code surface packet ships, with the min median and max per class and any files below the 70 bar.
trigger_phrases:
  - 'dqi baseline'
  - 'doc quality scores'
  - 'dqi below bar'
  - 'readme score table'
  - 'doc score table'
importance_tier: normal
contextType: general
version: 1.0.0.0
---

# Pi Remote DQI Baseline

The last measured Document Quality Index for every markdown doc this sk-code surface packet ships.

---

## 1. OVERVIEW

### Purpose

Record the current-state DQI for the packet's own documentation set — `SKILL.md`, `README.md`, and every
file under `references/`, `assets/`, and `manual-testing-playbook/` — so a docs change can prove it kept
the set above the bar rather than guessing.

### When to Use

- Before a docs change on this packet, to know the score it must hold
- After editing any packet doc, to confirm the file still clears the bar of 70
- When reviewing the packet's documentation health at a glance

### Core Principle

This baseline measures the docs that ship inside this packet, post-migration. The design-system docs that
once lived under the app's `docs/` now live under this packet's `references/`; the app's own module
READMEs (`app-mobile/`, `app-relay/`, and the rest) are measured in the app repo and are out of this
packet's baseline.

### Key Sources

- `python3 .opencode/skills/sk-doc/scripts/extract_structure.py <file>` — the deterministic DQI scorer
- [README.md](./README.md) — the quality-folder overview this baseline sits in

---

## 2. SUMMARY

The scores come from the sk-doc structure extractor run with
`python3 .opencode/skills/sk-doc/scripts/extract_structure.py <file>`. The sweep covered 48 markdown files
across the packet — `SKILL.md`, both READMEs, 30 reference docs, 7 asset checklists, and 8
manual-testing-playbook files — on 2026-08-27. The `changelog/` release notes are excluded from the sweep.

| Metric                    | Value |
| ------------------------- | ----- |
| Files measured            | 48    |
| Reference docs            | 30    |
| Asset checklists          | 7     |
| Playbook files            | 8     |
| Packet root + READMEs      | 3     |
| Lowest score              | 78 |
| Median score              | 89 |
| Highest score             | 97 |
| Files below the bar of 70 | 0 |

Per class:

| Class                     | Files | Min     | Median  | Max     |
| ------------------------- | ----- | ------- | ------- | ------- |
| Reference docs            | 30    | 87 | 91 | 97 |
| Asset checklists          | 7     | 82      | 85      | 88      |
| Playbook files            | 8     | 78      | 85      | 87      |
| Packet root + READMEs      | 3     | 81      | 83      | 86      |
| All files                 | 48    | 78  | 89  | 97  |

---

## 3. PACKET ROOT AND READMES

| File                          | Type   | DQI | Band |
| ----------------------------- | ------ | --- | ---- |
| SKILL.md                      | skill  | 86  | good |
| README.md                     | readme | 83  | good |
| references/quality/README.md  | readme | 81  | good |

---

## 4. REFERENCE SCORES

| File                                                        | DQI | Band      |
| ----------------------------------------------------------- | --- | --------- |
| references/a11y-parity.md                                   | 91  | excellent |
| references/browser-free-verification-recipe.md              | 95  | excellent |
| references/comment-grammar.md                               | 97  | excellent |
| references/component-story-upkeep.md                        | 95  | excellent |
| references/component-tokens.md                              | 87  | good      |
| references/css-class-naming-bem.md                          | 89  | good      |
| references/editability-guardrails.md                        | 89  | good      |
| references/folder-docs.md                                   | 89  | good      |
| references/retint-recipes.md                                | 91  | excellent |
| references/scoped-style-ownership.md                        | 91  | excellent |
| references/skill-reference-integrity.md                     | 91  | excellent |
| references/svelte-runes-effects.md                          | 89  | good      |
| references/theme-remap.md                                   | 87  | good      |
| references/token-library.md                                 | 92  | excellent |
| references/verification.md                                  | 90  | excellent |
| references/workflow-debug.md                                | 91  | excellent |
| references/workflow-implement.md                            | 92  | excellent |
| references/workflow-verify.md                               | 91  | excellent |
| references/operations/incident-playbooks.md                 | 89  | good      |
| references/operations/operations.md                        | 97  | excellent |
| references/operations/rollback.md                          | 95  | excellent |
| references/release/ai-deploy-playbook.md                   | 97  | excellent |
| references/release/release-verification.md                 | 97  | excellent |
| references/setup/install-and-onboarding.md                 | 89  | good      |
| references/setup/setup.md                                  | 96  | excellent |
| references/standards/code-standards.md                     | 88  | good      |
| references/standards/platform-support.md                   | 91  | excellent |
| references/standards/security.md                           | 91  | excellent |
| references/quality/dqi-baseline.md                         | 93 | excellent |
| references/quality/pi-remote-full-access-runtime-baseline.md | 97  | excellent |

---

## 5. ASSET AND PLAYBOOK SCORES

| File                                                  | Type    | DQI | Band |
| ----------------------------------------------------- | ------- | --- | ---- |
| assets/token-retint-checklist.md                      | asset   | 88  | good |
| assets/bem-rename-checklist.md                        | asset   | 87  | good |
| assets/ds-verification-checklist.md                   | asset   | 87  | good |
| assets/a11y-parity-checklist.md                       | asset   | 85  | good |
| assets/runes-effect-audit-checklist.md                | asset   | 85  | good |
| assets/guardrail-audit-checklist.md                   | asset   | 84  | good |
| assets/story-coverage-checklist.md                    | asset   | 82  | good |
| manual-testing-playbook/comment-convention-routing.md | generic | 87  | good |
| manual-testing-playbook/token-edit-routing.md         | generic | 87  | good |
| manual-testing-playbook/accessibility-routing.md      | generic | 85  | good |
| manual-testing-playbook/guardrail-routing.md          | generic | 85  | good |
| manual-testing-playbook/language-standards-routing.md | generic | 85  | good |
| manual-testing-playbook/debugging-routing.md          | generic | 84  | good |
| manual-testing-playbook/verification-routing.md       | generic | 84  | good |
| manual-testing-playbook/manual-testing-playbook.md    | generic | 78  | good |

---

## 6. BELOW THE BAR

No file scores below the bar of 70. The lowest score in the packet is 78
(`manual-testing-playbook/manual-testing-playbook.md`), a short routing-recall index whose word count sits
under the README word range. Every other file clears the bar with room to spare.

The bar of 70 is the acceptable floor; 90 is the excellent target. Files between the two are already
usable and improve mainly by adding worked examples or expanding thin sections.

---

## 7. MEASUREMENT NOTES

- The extractor scores every file deterministically. Re-runs on the same file return the same DQI.
- The word range for README-class files is 500 to 3000 words; short files lose word-count points first.
- The heading density range is 2.0 to 8.0 H2 sections per 500 words. Short files with many H2 sections
  lose heading points.
- The style component grants points for an intro paragraph after the H1 and for numbered ALL-CAPS H2s.
- Re-run the sweep after any packet docs change, and before a packet release, to refresh this table.

---

## 8. REFERENCES AND RELATED RESOURCES

- [README.md](./README.md) — the quality-folder overview
- [pi-remote-full-access-runtime-baseline.md](./pi-remote-full-access-runtime-baseline.md) — the other quality baseline
- [skill-reference-integrity.md](../skill-reference-integrity.md) — the cross-repo path guard the docs also pass
