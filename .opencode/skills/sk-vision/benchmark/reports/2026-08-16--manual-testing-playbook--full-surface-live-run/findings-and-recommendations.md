# Findings And Recommendations

> sk-vision · doc · ndjson-runtime-stdin · moondream2 · full-surface-live-run

No FAIL verdicts were recorded, so this run yields no required remediation.

## Observations

- Keep segment documented as unavailable on the default Moondream 2 checkpoint; validate it separately only when a Moondream 3 runtime is configured.
- Use concrete object nouns for detect and point. On this fixture, `text` and `ERROR` returned no detections while `word` returned a normalized box.
- The shipped reverse handler performs a directory scan rather than requiring a prebuilt index. With `providers=local` and the phase scratch directory, it found the copied fixture at similarity 1.0.
