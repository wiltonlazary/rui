# Design discipline

AI programming is full of naive designs that compound over time. Design as a
systems engineer: reason in nanoseconds, and treat every allocation,
encoding, hop and wake-up on a hot path as a defect until it has a written
cost and a measurement.

- No encoding on hot paths or at boundaries: no JSON, no string-built keys,
  no per-operation records crossing a layer. Use integer ids, fixed-shape
  records or typed columns, rings and plain calls. ren-core's worker wire is the
  model:
  - Int32 op buffers, a number column and a per-message string table;
  - buffers transferred and returned;
  - flat event arrays.
- No hop without a reason: a microtask, promise, timer, frame wait, worker
  message or reply is a measured, configurable choice, never a default.
- No timer to paper over a design gap. A `setTimeout`
  (or a frame count, a sleep, a retry loop) that waits for something to
  settle hides a missing signal: wait on the event or state that says it
  is done (an animation's `finished`, a `transitionend`, a layout's
  callback, a flush, `$moves`/`$resizes`), or fix the owner so it reports
  it. Tests and checks follow the same rule: poll a condition with a bound
  only when no event exists, never sleep a guessed duration.
  A timer that waits for something to settle is naive
  design and an anti-pattern, never a fix. Every repository enforces it
  with `test/timers.test.js`, part of `npm test`. Each `setTimeout`,
  `setInterval`, `setImmediate`, `requestIdleCallback` or
  `requestAnimationFrame` call (a frame count is a timer too) carries a
  `timer: <kind>` comment saying why it is a timer:
  - `feature`: the delay is the behaviour (an interval, a debounce, an
    auto-reset, a demo's simulated latency, an animation's or a
    measurement's frame loop);
  - `deadline`: a bound that gives up or fails, never the path to success;
  - `fallback`: a scheduler's stand-in where the host lacks the signal
    (no requestAnimationFrame, no event for a write to shared memory);
  - `poll`: a bounded loop re-checking a condition that has no event;
  - `turn`: ends the job so the engine does what it does only between
    jobs (V8 releases what a WeakRef held), never to let work settle.

  A sleep helper (`sleep`, `delay`, `pause`, `tick`, `wait` made of a timer)
  fails whatever it says. The guard is the same file in every repository;
  change it in all of them at once.
- Batching, priority and QoS belong to the graph and the renderer, not to
  callers' ad hoc queues:
  - batching: one `$batch` per event task and coalesced `$flush`;
  - priority: lanes for urgent and `$defer`red work, with no starvation;
  - QoS: coalescing per node and property, backpressure (one message in
    flight), frame-sized slices, and overtaking.

  Keep them measured.
- Bindings and rows allocate nothing avoidable: closure-free `$bind`, one
  runner per row, lazy row signals (`$rows`), text through `.data`, and
  minimal keyed moves.
- Keep V8's shapes monomorphic: fixed-shape constructors, module-local
  constants, small inlinable hot functions. Each project's README records
  every deliberate choice of this kind with its measured effect. Revert what
  does not win in a paired run.

## Self-contained text

- Code, comments, JSDoc, READMEs, TODOs, tests and demo data describe what
  the code does and why, in the present tense, each behaviour and format on
  its own terms.
- Other software is named where it is used: dependencies and peers (Taffy,
  CanvasKit, three.js, esbuild, TypeScript and its option values), web
  standards, and the libraries ren-bench measures.
- The browser engine and its font rasterizer (e.g. Blink, FreeType), which
  the drawn renderers match for DOM parity, are the platform and are named,
  like Chrome and WebKit.
- Public names are plain words, one word per concept across the family
  ([stability.md](stability.md), "Names").

## Full JavaScript performance

The repositories are free to use bitwise operations and every JavaScript
feature for full performance. Use whatever makes the hot paths fastest,
measured:
- bitwise flag words and packed integer state (`|0`, `>>>`, masks);
- typed arrays (`Int32Array`, `Uint8Array`, `Float64Array`) as columns and rings;
- `SharedArrayBuffer` and `Atomics` (for example the worker mailbox);
- monomorphic shapes and small hot functions the JIT can inline;
- object pools, preallocated buffers, and `WeakRef`/`FinalizationRegistry`
  where they pay off;
- any modern platform API with a measured win.

Each change must be proved by the apples-to-apples benchmarks
([benchmarks.md](benchmarks.md)) and keep every test green.
