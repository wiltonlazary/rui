# rui

This is the root layer of the rui workspace, a family of JavaScript UI
libraries.
An agent started here manages and develops the projects below it. An agent
started inside a project follows that project's `AGENTS.md`, which points back
here for the shared rules.

rui is JavaScript only: code, tests, benchmarks, scripts and tooling are all
JavaScript, and no other language or toolchain is used here.

## Projects

| Project | What it is | Checkout |
| --- | --- | --- |
| [rrx-core](https://github.com/wiltonlazary/rrx-core) | the reactive core: signals, memos, effects, scopes, contexts, tasks, stores, keyed lists on an integer-id graph (package `rrx-core`) | `rrx-core/wtree/_repo_` |
| [ren-core](https://github.com/wiltonlazary/ren-core) | the renderer-neutral component layer: JSX and the `ren-core/esbuild` compiler, application roots, contexts, background disposal, worker runtime, and the Host contract renderers implement (package `ren-core`, depends on rrx-core) | `ren-core/wtree/_repo_` |
| [ren-dom](https://github.com/wiltonlazary/ren-dom) | the DOM renderers `$dom` and `$shadow` (package `ren-dom`, depends on ren-core) | `ren-dom/wtree/_repo_` |
| [ren-canv](https://github.com/wiltonlazary/ren-canv) | the engine of the drawn renderers: a retained scene of the portable primitives with its own layout, portable style, text, paint command buffer, damage, hit testing, input, editing, selection, native text entry and accessibility mirror; renderers replay its frames (package `ren-canv`, depends on ren-core) | `ren-canv/wtree/_repo_` |
| [ren-skia](https://github.com/wiltonlazary/ren-skia) | the Skia (CanvasKit) renderer: ren-canv's engine replayed on a CanvasKit surface, with CanvasKit fonts (package `ren-skia`, depends on ren-canv and ren-core; CanvasKit is a peer) | `ren-skia/wtree/_repo_` |
| [ren-three](https://github.com/wiltonlazary/ren-three) | the three.js renderer: JSX over a scene graph, raycast events, frames and `$animate`, and the portable primitives through ren-canv (an overlay and panels) (package `ren-three`, depends on ren-canv and ren-core; three.js is a peer) | `ren-three/wtree/_repo_` |
| [ren-html](https://github.com/wiltonlazary/ren-html) | a view rendered once to HTML, ren-dom's exact markup, for servers and static pages; no hydration (package `ren-html`, depends on ren-core and ren-dom) | `ren-html/wtree/_repo_` |
| [ren-json](https://github.com/wiltonlazary/ren-json) | a view rendered once to what it means, as data only (nodes by ARIA role, microdata items; wrappers, styles and behaviour dissolved), for AI agents, tools, servers, APIs and native clients (package `ren-json`, depends on ren-core) | `ren-json/wtree/_repo_` |
| [rui-core](https://github.com/wiltonlazary/rui-core) | rui's components: a complete set (layout, inputs, combobox, buttons, navigation, feedback, overlays, data display, typography), hooks as rrx-core operations, forms and extensions (dates, charts, notifications, launcher, carousel, dropzone, modals, snippet, load bar, editor), on ren-core's portable primitives only, so each renders the same on every renderer (package `rui-core`, depends on ren-core and rrx-core) | `rui-core/wtree/_repo_` |
| [rui-show](https://github.com/wiltonlazary/rui-show) | rui-core's documentation and live showcase: every component, hook and extension with demos switchable between the DOM, Skia and three.js renderers and between themes, checked in headless Chrome (private application, depends on rui-core, ren-core and the renderers) | `rui-show/wtree/_repo_` |
| [ren-show](https://github.com/wiltonlazary/ren-show) | a live tour of every rrx-core and ren-core feature, one demo and test per feature group, all renderers together on one page, checked in headless Chrome (private application, depends on rrx-core, ren-core, ren-canv, the three renderers, ren-html and ren-json) | `ren-show/wtree/_repo_` |
| [ren-bench](https://github.com/wiltonlazary/ren-bench) | the shared reactivity, DOM market, worker UI, Skia and three.js benchmark harnesses, pinned competitors and recorded results (private tooling) | `ren-bench/wtree/_repo_` |

ren-bench owns the benchmark code, dependencies and results. Libraries keep
their implementation, behavioral tests and performance defect TODOs.

The projects divide the work so each stays light and all of them live
together when imported:

- **Layout.** ren-canv lays out with Taffy (`taffy-layout`, pinned), on the
  main thread or in a layout worker; ren-skia and ren-three use it only. The
  DOM keeps the browser's layout unless it opts in to Taffy (ren-dom's
  `layout` option, main thread or worker). Taffy sits behind ren-canv's
  neutral boundary (`src/engine.js`: commands with plain CSS boxes); only
  ren-canv's `src/taffy.js` imports `taffy-layout`, and `useLayout` sets
  another engine, so it can be replaced or reimplemented without touching
  the rest of the family. Taffy is chosen for speed; ren-canv's README gives
  its measured cost.
- **Drawn renderers are first class.** ren-skia and ren-three run the basic
  components fully, text selection included, so an application can be
  built on any renderer alone or on several mixed. The canvases take input
  as the DOM does (event names, fields, bubbling, capture, focus, scroll,
  cursor; ren-canv's README), and every renderer but ren-json (which has no
  visuals) takes `setTheme`. The primitives and their portable style
  (ren-core's `style`) render the same on ren-dom, ren-skia and ren-three;
  ren-canv is the drawn renderers' shared engine. ren-show proves it with
  rich applications: Mail runs one client on every renderer with a parity
  check, Showroom lives in a three.js root and Terminal on Skia.
- **Components.** rui-core holds the complete set of components and
  extensions, and rui-show shows them. rui-core builds on ren-core's
  portable primitives only; a gap it meets lands in ren-core or ren-canv
  first.
- **Server output.** Server HTML is ren-html (`$html`). ren-json exports the
  content as data, not the visuals: AI agents and other tools navigate and
  understand it through a data-only hierarchy. Meaning is marked with ARIA
  roles and microdata (ren-core's `Meaning` props); ren-json keeps what they
  mark and dissolves visual-only tags, properties and hierarchy, and
  rui-core's components carry the marks.

Each project uses the same layout:
- `<project>/README.md`: a short orientation, outside the git repo.
- `<project>/wtree/_repo_/`: the git repository on `main`. Feature branches
  are sibling worktrees `wtree/<name>`.
- `<project>/build/<worktree>_<version>/`: each worktree's build output,
  reached from the checkout through its ignored `artifact` link.

A new project follows the same layout. It also gets:
- an `AGENTS.md` with its own rules and a pointer to this layer;
- a row in the table above.

## Read first

1. This file.
2. The shared rules in [.ai/config](.ai/config):
   - [javascript.md](.ai/config/javascript.md): production JavaScript, types and tests.
   - [design.md](.ai/config/design.md): design discipline and full JavaScript performance.
   - [benchmarks.md](.ai/config/benchmarks.md): performance targets and apples-to-apples measurement.
   - [concepts.md](.ai/config/concepts.md): one Root, contexts and naming.
   - [resources.md](.ai/config/resources.md): memory caps and the machine-wide Chrome lock.
   - [stability.md](.ai/config/stability.md): the public API is frozen;
     its scope; what may change; how the export locks guard the contract.
     Read it first.
   - [git.md](.ai/config/git.md): commits in each repository.
3. The project's `AGENTS.md`, `README.md` and `TODO.md` before you change it.

## Managing the workspace

The root layer owns work that spans projects:

- **Cross-project changes.** When ren-core needs something new from rrx-core, it lands
  in rrx-core first, with rrx-core's tests. Then ren-core is updated and both suites pass. A
  change to rrx-core's exports, its `rrx-core/core` or `rrx-core/context` hooks, or any
  behaviour ren-core relies on is not finished until `npm test` passes in ren-core,
  rui-core (which uses rrx-core's operations through ren-core) and ren-show
  (whose apps use them directly).
  The same holds one level down: ren-core never imports a renderer; when a
  renderer needs something new from ren-core (a Host member, a `mount` option),
  it lands in ren-core first, and a change to ren-core (the Host contract, `mount`,
  `skeleton`, the worker runtime) is finished once ren-dom, ren-canv, ren-skia,
  ren-three, ren-html, ren-json, rui-core and ren-show pass; a change to ren-canv is
  finished once ren-dom, ren-skia, ren-three and rui-core pass; a change to
  ren-dom is finished once ren-html, ren-skia, ren-three and rui-core pass;
  a change to ren-json or ren-skia is finished once rui-core passes too. Commit in each repository separately. Each commit message names what the
  other repository needs.
- **Order.** Install, test and release rrx-core, then ren-core, then ren-canv,
  then ren-dom (its tests use ren-canv's Flow), then ren-html and ren-json,
  then ren-skia and ren-three (their tests use ren-dom), then ren-show, then
  rui-core, then rui-show, then ren-bench. Each library install generates its declarations
  (`prepare`), which the `tsc` of those after it reads. A
  change that ren-show demonstrates is finished once ren-show's
  `npm test` (and, for renderers, its headless Chrome check) passes too.
- **Versions.** The nine libraries (rrx-core to rui-core) are published on
  npm as `@maxenve/<name>` under the MIT license and share one version line
  (0.1.11); the applications and ren-bench stay private. Code imports the
  bare names (`ren-core`), and consumers install under them
  (`npm install ren-core@npm:@maxenve/ren-core`). The checkouts keep their
  `file:` links. A release, in the order above, packs each library
  (`npm pack`), names the packed `package.json` `@maxenve/<name>`, sets every
  `file:` link in it to the alias `npm:@maxenve/<name>@<version>`, and publishes
  that tarball (`npm publish <tarball> --access public`); a library's
  dependencies are on npm before it is. Nothing is published or pushed
  unless the user asks.
- **Benchmarks across the family.** rrx-core, and ren-core on top of it, are measured
  against the market libraries; see [benchmarks.md](.ai/config/benchmarks.md).
- **Keep this layer light.** Real artifacts are code, tests and docs that
  match the code. Put a project's open work in its `TODO.md` and shared
  rules here; nothing else is kept.

Code, comments, docs, tests and demo data state what the code does, on its
own terms and in the present tense ([design.md](.ai/config/design.md),
"Self-contained text").

## Changing the shared rules

A rule that applies to several projects belongs in `.ai/config`, not in
copies across `AGENTS.md` files. Change it here and commit it in this layer's repository.
State each rule as it holds, in the present tense.
