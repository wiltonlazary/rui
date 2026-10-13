# rui

A family of JavaScript UI libraries:

- [rrx-core](rrx-core/README.md) is the reactive core.
- [ren-core](ren-core/README.md) is the renderer-neutral component layer, built on rrx-core.
- Its renderers, each a package of its own: [ren-dom](ren-dom/README.md)
  ($dom and $shadow), [ren-skia](ren-skia/README.md) (CanvasKit) and
  [ren-three](ren-three/README.md) (three.js); the drawn ones share
  [ren-canv](ren-canv/README.md), their engine. The primitives and their
  portable style render the same on each.
- Server renders, without hydration: [ren-html](ren-html/README.md)
  (HTML) and [ren-json](ren-json/README.md) (data only).
- [ren-show](ren-show/README.md) is a live tour of the rrx-core and ren-core features,
  with all three renderers on one page.
- [rui-core](rui-core/README.md) is rui's component library, a complete
  set on the portable primitives, the same on every renderer;
  [rui-show](rui-show/README.md) is its documentation and live showcase.
- [ren-bench](ren-bench/README.md) owns the reactivity, DOM market, worker UI,
  Skia and three.js benchmarks and their recorded results.

Everything here is JavaScript. Agents start with [AGENTS.md](AGENTS.md), and
the shared rules are in [.ai/config](.ai/config). Each project is its own git
repository under `<project>/wtree/_repo_`. This directory is a small
repository of its own that tracks only this layer.
