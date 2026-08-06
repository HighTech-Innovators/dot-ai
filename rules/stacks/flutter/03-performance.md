# Flutter Performance

Baseline rules for keeping the UI responsive. Scale scrutiny to
what the app actually does — do not pre-optimise a static screen.

---

## Widgets and rebuilds

- **`const` every widget whose constructor arguments are all
  compile-time constant** (see `01-constraints.md`). This is the
  single highest-leverage rule here — it lets Flutter skip
  rebuilding subtrees that haven't changed.
- **Extract widgets instead of writing large `build()` methods.**
  A smaller widget rebuilds independently of its siblings; a
  500-line `build()` rebuilds everything on every change.
- **Do not reach for manual memoization tricks** (caching build
  output in fields, etc.) before the extraction above. Measure
  before adding complexity.

## Lists

- **Unbounded or long lists use `ListView.builder` /
  `GridView.builder`.** A `Column`/`Row` built from a `.map()` over
  an unknown-length collection is a bug, not a shortcut.
- **List items get a stable `Key`** only when the list can reorder,
  insert, or delete mid-list. Static lists don't need one.

## Work off the build method

- **No I/O, parsing, or heavy computation inside `build()`.**
  Load data in `initState`/async setup and hold the result in
  state; render it with `FutureBuilder`/`StreamBuilder` or plain
  conditional rendering once it arrives.

## Images

- **Specify explicit `width`/`height`** on images so layout does
  not shift when they load.
- **Ship assets sized for the device**, not the largest case a
  design tool exported. Use `precacheImage` for anything shown the
  instant a screen appears.

## Measuring

- **A performance claim comes with a measurement** — the Flutter
  DevTools performance/timeline view, not a subjective "feels
  smoother."
