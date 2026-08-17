# Skill Benchmark Report - sk-vision

> Rendered from `skill-benchmark-report.json`. Scoring: `not-applicable-manual-outcome`; trace mode: `doc`.

**Verdict: PASS**

## Dimension Scores

| Dimension | Weight | Score |
|---|---|---|
| D1 inter (advisor) | - | _not-applicable-manual-outcome_ |
| D1 intra (router) | - | _not-applicable-manual-outcome_ |
| D2 discovery | - | _not-applicable-manual-outcome_ |
| D3 efficiency | - | _not-applicable-manual-outcome_ |
| D4 usefulness | - | _not-applicable-manual-outcome_ |
| D5 connectivity | - | _not-applicable-manual-outcome_ |

## Provenance And Execution Context

| Field | Value |
|---|---|
| Skill root | `.opencode/skills/sk-vision` |
| Captured at | 2026-08-16T16:30:00.000Z |
| Executor / model | ndjson-runtime-stdin / moondream2 |
| Runtime | Python 3.12.11 on Apple Silicon MPS |
| Fixture | `specs/sk-vision/001-sk-vision-fork-of-opencode-senses/009-manual-testing-playbook/scratch/fixture.png` |

## Scenarios

| Scenario | Tool | Verdict | Observed result |
|---|---|---|---|
| VSN-001 | inspect | PASS | caption + scene + ocr all non-empty |
| VSN-003 | detect | PASS | one normalized box for target `word` after bounded retry |
| VSN-004 | point | PASS | one normalized point |
| VSN-005 | segment | SKIP | moondream2 has no segment template |
| VSN-006 | colors | PASS | palette, buckets, average RGB |
| VSN-007 | diff | PASS | correct zero-change result for copied fixture |
| VSN-008 | metadata | PASS | PNG, 480x140, RGB, 1284 bytes |
| VSN-009 | crop | PASS | existing 240x70 output |
| VSN-010 | zoom | PASS | existing 960x280 output at 2x |
| VSN-011 | annotate | PASS | existing annotated 480x140 output |
| VSN-013 | reverse | PASS | local match similarity 1.0 |

## Methodology And Caveats

- One runtime process loaded moondream2 once, then received the full method sequence over NDJSON stdin.
- Detect received one bounded retry because generic `text` returned no boxes; target `word` produced a valid detection.
- Diff used a copied fixture-b, so a zero-change result is the expected result.
- Segment is an accepted SKIP because the default checkpoint lacks the required Moondream 3 segment template.
- The harmless `MOONDREAM_API_KEY is not set` warning and Photon telemetry shutdown warning were emitted on stderr and did not change any response envelope.
