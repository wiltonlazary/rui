# Shared concepts and names

## One Root, contexts

There is one root concept for every project. Never define two things called
Root, and never make users import one under an alias.

- `$root(id)` is rrx-core's graph; ren-core's `Root('id')` returns exactly
  that graph, and `Root(options)`/`<Root>` extend it. The id matches
  `[a-z][a-z0-9-]{0,31}`, and everything above the graph derives from it.
- ren-core's `Root(options)` and `<Root>` start an application root on a graph of
  its own, keyed by the same id. They extend that graph; they do not wrap it.
  The id keys:
  - storage keys `ren:<id>:*` and lock names;
  - element ids, through ren-core's `$name`;
  - the host element's `data-ren-root`;
  - events `ren:*` on the host element.

  Nothing is added to globals.
- Roots are isolated from each other: separate graphs, pending state,
  contexts, storage, locks and DOM. A nested root inherits nothing unless it
  is passed explicitly. Only immutable library code is shared.
- Contexts are rrx-core's. `$context(fallback)` is its own provider component.
  Every operation has exactly two spellings:
  - root-first: `$lookup(root, context)`, `$update(root, context, value | fn)`,
    `$entries(root)`;
  - short, using the running root: `$lookup(context)`, and so on.

  ren-core adds the built-in read-only layers `Id`, `Storage`, `Locks`, `Runtime`,
  `Props` and `Send`, and `$name(root, name)` in the same two spellings.
- ren-core's concepts reuse rrx-core's names (root, scopes and owners, contexts, ids)
  instead of wrapping them under new ones.

## Renderers and the worker

Renderers are packages of their own (ren-dom, ren-skia, ren-three), and a
renderer always lives on the main thread. In worker mode the worker holds
only the application and its components (rrx-core, ren-core's component layer and
`ren-core/worker`): no DOM, no canvas, no CanvasKit, no three.js and no renderer
package code, for every renderer. What a renderer needs from the worker
crosses as records (`construct` values for ren-three), and a worker bundle is
built on its own, never sharing a chunk with the page. three.js, its
scenes and the DOM live on the main thread only.

## Names

- The error prefixes name the package: `RRX:` (rrx-core), `REN:` (ren-core
  and ren-dom's shared paths), `REN <name>:` for ren-core's worker runtime, its compiler and
  the renderers' own roots (`REN worker:`, `REN remote:`, `REN esbuild:`, `REN dom:`,
  `REN shadow:`, `REN canv:`, `REN skia:`, `REN three:`, `REN html:`), `RUI:` (rui-core), and `RRX dev:` and
  `REN dev:` for development-mode diagnostics.
- Record each project's deliberate design choices in its README
  (§Design choices), with the measured effect.
