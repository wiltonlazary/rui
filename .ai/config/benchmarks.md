# Performance targets and benchmarking

## Targets

- rrx-core is on par with or faster than the fastest library measured, on
  every scenario of ren-bench's `bench/reactivity/run.mjs`.
- Bare ren-core on the DOM is on par with or faster than the fastest library
  in ren-bench's market table (`bench/market`), with ren-core and rrx-core
  underneath it.
- A regression against either target is a bug.
- The public API is frozen ([stability.md](stability.md)): the targets are
  tracked, not chased, and a gap stays parked unless the user asks.
- Adopt whatever measures better, with tests.

## Apples to apples

- Every implementation runs the same definition:
  - the same scenario graphs, or the same markup, data and operations;
  - the same warm-up and number of timed rounds;
  - the same method.

  Each runs in its own process or browser, with the same flags.
- Results carry checksums, or the harness validates the table after every
  operation. A wrong answer is a failure, never a time.
- Record provenance with every reading: node or Chrome version, library
  versions, flags, load and commit. Compare against stored results only when
  they were taken with the same method.
- Measure at low load. Check `uptime` and `free -g` first. Pair A/B runs, in
  ABBA order, for any claim smaller than the harness noise.
- ren-bench's `bench/reactivity/scenarios.json` is the canonical scenario definition: every
  library runs it unchanged. `RRX_SCENARIOS=<path>` runs another definition
  file.

## Harnesses

| Harness | Project | Measures |
| --- | --- | --- |
| `bench/reactivity/run.mjs`, `bench/reactivity/sweep.mjs` | ren-bench | reactive core against alien-signals, Preact signals, Vue reactivity, Solid 1.9 and `@solidjs/signals` 2; background disposal |
| `bench/market/run.mjs` | ren-bench | keyed js-framework-benchmark table against Solid 2, Solid 1.9, Svelte 5, Vue 3.5, Preact, Lit, React and vanilla |
| `bench/ui/run.mjs` | ren-bench | worker against direct mode: lanes, coalescing, latency percentiles, disposal under input (on ren-dom and ren-skia) |
| `bench/skia/frames.jsx`, `bench/skia/chrome.mjs` | ren-bench | per-frame cost, damage repaint, the accessibility mirror |
| `bench/r3f/run.mjs` | ren-bench | the same scene of 1,000 meshes against react-three-fiber: create, update every 10th, swap, clear, to the render that draws each |

ren-bench owns every harness, its dependencies and its results. A run
reports the ren-bench commit and the versions, commits and dirty states of
the family packages actually measured; a stored result file keeps the
environment and method of its run. Performance defects stay
with their library owner; benchmark infrastructure defects belong to ren-bench.

Browser harnesses follow [resources.md](resources.md): the machine-wide
Chrome lock, and one browser at a time.
