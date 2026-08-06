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

## Rendering costs

- **`saveLayer()` is expensive** — it allocates an offscreen buffer
  and can force a GPU render-target switch. Widgets that shouldn't
  need it can still trigger it indirectly: `ShaderMask`,
  `ColorFilter`, `Chip` (when `disabledColorAlpha != 0xff`), `Text`
  with an `overflowShader`, and anything using
  `Clip.antiAliasWithSaveLayer`. Check the DevTools timeline
  (`checkerboardOffscreenLayers`) before assuming a slow frame is
  something else.
- **`Opacity` is expensive; avoid it in animations.** Use
  `AnimatedOpacity` or `FadeInImage` for fading instead of animating
  an `Opacity` widget directly. For a static semi-transparent shape
  or block of text, draw it with a semitransparent color rather
  than wrapping it in `Opacity` — faster, as long as nothing in the
  shape overlaps itself.
- **Clipping is costly but cheaper than `Opacity`** (it doesn't call
  `saveLayer()` unless you pass `Clip.antiAliasWithSaveLayer`).
  Clipping defaults to `Clip.none` — only enable it where actually
  needed, and never inside an animation (pre-clip before animating
  instead). Prefer a widget's `borderRadius` property over a
  clipping rectangle for rounded corners.
- **Avoid overriding `operator ==` on `Widget` subclasses.** It
  looks like a way to dodge unnecessary rebuilds, but it produces
  O(N²) comparison behavior and defeats compiler assumptions that
  make widget calls fast. Rely on `const` and widget extraction
  (above) instead.

## String building

- **Use `StringBuffer`, not repeated `+`/string interpolation, when
  building a string from many parts** (especially inside a loop).
  Each `+` allocates a new `String`; `StringBuffer` collects the
  parts and concatenates once on `toString()`.

## Layout passes

- **An unbounded intrinsic-size lookup (e.g. sizing every grid cell
  to match the largest) forces a second, expensive layout pass.**
  Prefer a fixed cell size, or a custom `RenderObject` that sizes
  relative to one anchor child, over any layout that requires
  polling every child's intrinsic size. Use DevTools' *Track
  layouts* option to confirm whether a screen is taking extra
  passes before optimizing this.

## Frame budget

- **Build and render each frame in 16ms or less** on a 60Hz display
  (8ms build + 8ms render) — that's the threshold below which a
  frame is not visibly janky. Coming in well under that is still
  worth it: it improves battery life and thermal behavior, and
  matters more on 120Hz displays and low-end target devices, not
  just your dev machine.
- **When using `AnimatedBuilder`, don't put widgets that don't
  depend on the animation inside the `builder` callback** — that
  subtree rebuilds on every animation tick. Build it once and pass
  it in as `child` instead.
- **Avoid a concrete `List` of children (`Column([...])`,
  `ListView(children: [...])`) when most of that list isn't
  visible.** Use the `.builder` constructor (see Lists, above) so
  the build cost scales with what's on screen, not the full list.

## Measuring

- **A performance claim comes with a measurement** — the Flutter
  DevTools performance/timeline view, not a subjective "feels
  smoother."
