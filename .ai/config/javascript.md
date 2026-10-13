# Production JavaScript

The libraries (rrx-core, ren-core, ren-canv, ren-dom, ren-skia, ren-three,
ren-html, ren-json and rui-core) are production libraries for publication, for JavaScript and
TypeScript users alike.

- ESM, plain JavaScript with JSDoc types, checked by `tsc` with `strict` and
  `checkJs` (`npm run check`). The published `.d.ts` files are generated from
  the JSDoc (`scripts/types.mjs`, into the ignored `types/`). Handwritten
  declarations are the exception; ren-core's `src/jsx.d.ts` is the only one.
  The TypeScript consumers in each project's `test/types/` must keep
  compiling.
- No runtime dependencies beyond the family: rrx-core has none, and ren-core depends on
  rrx-core only; ren-canv also depends on its pinned `taffy-layout`. The renderers' peers
  (`canvaskit-wasm` for ren-skia, `three` for ren-three) are required by those
  packages; `esbuild` stays an optional peer. Dev
  tools and benchmark libraries are pinned to exact versions. ren-bench owns
  the benchmark dependencies; competitor suites have their own `package.json`
  and `node_modules`, including a separate install for an older competitor major.
- Short, simple, single-word public names. The portable primitives' names are
  independent of HTML. When a name changes, migrate every caller in every
  project; never add compatibility aliases or wrappers.
- Add a meaningful test for every behaviour change and newly found edge case.
  `npm test` is each project's gate: declarations, `tsc`, the type tests and
  node:test (ren-core runs its JSX suites twice, through the runtime and compiled
  by `ren-core/esbuild`, and both passes must agree). Report its counts verbatim.
- A test that opens a port, worker, server or window closes it in `t.after`
  (a `finally` waits on a hung await) and bounds any wait for an answer with
  `{ timeout }`, so a failure ends the run instead of hanging it. Never use
  `--test-force-exit`: it can end a run with exit 0 before a file's last
  results arrive. A happy-dom `Window` is closed with `happyDOM.close()` and
  awaited; its `window.close()` frees nothing. node:test keeps every hook until
  the run ends, so a `t.after` hook must not hold a large object once it has
  run: keep the close in a slot the hook clears.
- Tooling is JavaScript: no Python and no shell scripts in the repositories.
