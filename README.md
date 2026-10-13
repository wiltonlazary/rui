# rui

A family of JavaScript UI libraries:

- [rrx-core](https://github.com/wiltonlazary/rrx-core) is the reactive core.
- [ren-core](https://github.com/wiltonlazary/ren-core) is the renderer-neutral component layer, built on rrx-core.
- Its renderers, each a package of its own: [ren-dom](https://github.com/wiltonlazary/ren-dom)
  ($dom and $shadow), [ren-skia](https://github.com/wiltonlazary/ren-skia) (CanvasKit) and
  [ren-three](https://github.com/wiltonlazary/ren-three) (three.js); the drawn ones share
  [ren-canv](https://github.com/wiltonlazary/ren-canv), their engine. The primitives and their
  portable style render the same on each.
- Server renders, without hydration: [ren-html](https://github.com/wiltonlazary/ren-html)
  (HTML) and [ren-json](https://github.com/wiltonlazary/ren-json) (data only).
- [ren-show](https://github.com/wiltonlazary/ren-show) is a live tour of the rrx-core and ren-core features,
  with all three renderers on one page, at <https://wiltonlazary.github.io/ren-show/>.
- [rui-core](https://github.com/wiltonlazary/rui-core) is rui's component library, a complete
  set on the portable primitives, the same on every renderer;
  [rui-show](https://github.com/wiltonlazary/rui-show) is its documentation and live showcase,
  at <https://wiltonlazary.github.io/rui-show/>.
- [ren-bench](https://github.com/wiltonlazary/ren-bench) owns the reactivity, DOM market, worker UI,
  Skia and three.js benchmarks and their recorded results.

Everything here is JavaScript. Agents start with [AGENTS.md](AGENTS.md), and
the shared rules are in [.ai/config](.ai/config). Each project is its own git
repository under `<project>/wtree/_repo_`. This directory is a small
repository of its own that tracks only this layer.
