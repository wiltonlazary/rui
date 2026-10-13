# Stability

The project is stable so the work stays on business problems, not on the
libraries themselves. The public API of the libraries (rrx-core, ren-core,
ren-dom, ren-canv, ren-skia, ren-three, ren-html, ren-json, rui-core) is
frozen: they follow the rules below, and their export locks are the
contract. The work goes to applications.

## Scope

- The server renders with ren-html (`$html`: HTML, ren-dom's exact
  markup) or ren-json (`$json`: the view's data only, everything that is
  not data stripped). Both render once, after the view's tasks settle
  (ren-core's `$settle`), then dispose.
- The live graph runs on the client only: a client that needs the live
  application renders it with ren-dom.
- The benchmarks measure rrx-core and ren-dom against every library in
  ren-bench.

## Names

Public names are short, plain words, with no aliases. A web, W3C or
ECMAScript standard word is used as the standard spells it, and ren-three
uses three.js's words for three.js's things.

Each concept is one word across the family. In rui-core: a count cap is
`limit`, "is showing" is `shown`, a calendar's period on view is `period`,
media take `source` and `label`, the main value is `value` with
`defaultValue` and `onChange`, direction is `orientation`, a state object
made outside is `store`, and clearing a collection is `clear`.

## What may change

- A defect: a wrong result, a crash, a leak, a broken guarantee, a security
  issue. Each fix comes with a test that fails before it and passes after.
- A platform break: a browser, CanvasKit or three.js change that stops a
  renderer working. The fix goes in that renderer package.
- An addition a real application needs, when the user asks for it. It adds
  exports; it never renames or removes one.

## What does not change

- Public names, signatures and behaviour. The export lists are locked by
  each package's `test/exports.test.js`, so a rename, a removal or an
  unplanned addition fails `npm test`. Changing a lock file is a contract
  change, made only when the user asks.
- The guarantees each README states (the Task contract, root isolation,
  background disposal, worker mode).
- No performance chasing, refactors, renames or new features on the
  initiative of an agent. A measurement that shows a regression is a defect;
  a possible gain is not a reason to touch a frozen library.

## Where the work goes

Applications. Product code imports the libraries' public entries and never
their files. A problem met in an application is first solved in the
application; only a defect or a real gap reaches a library, as above.
