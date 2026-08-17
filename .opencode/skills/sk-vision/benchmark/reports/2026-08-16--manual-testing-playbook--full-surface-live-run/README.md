# 2026-08-16--manual-testing-playbook--full-surface-live-run

> sk-vision · doc · ndjson-runtime-stdin · moondream2 · full-surface-live-run

**Verdict: PASS**

## Run

| Field | Value |
|---|---|
| Target skill | sk-vision |
| Scoring method | not-applicable-manual-outcome |
| Trace mode | doc |
| Executor | ndjson-runtime-stdin |
| Model | moondream2 |
| Scenarios | 11 |
| Outcome tally | 10 PASS, 1 SKIP, 0 FAIL |

## Files

| File | Contents |
|---|---|
| [`skill-benchmark-report.json`](./skill-benchmark-report.json) | Machine record from which the curated files derive |
| [`skill-benchmark-report.md`](./skill-benchmark-report.md) | Human-readable aggregate report |
| [`results.csv`](./results.csv) | One row per live tool scenario |
| [`failed-runs.md`](./failed-runs.md) | FAIL and SKIP details |
| [`findings-and-recommendations.md`](./findings-and-recommendations.md) | Findings grounded in the observed run |
| [`source.md`](./source.md) | Corpus and raw-evidence locations |

## Reading This Folder

This report covers the 11 public tools that lacked live evidence after phase 009. The overall verdict is PASS because every runnable tool passed and the single SKIP has the named Moondream 2 capability blocker required by the execution brief.
