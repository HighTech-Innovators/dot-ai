# Flutter Navigation

How screens are wired. No routing package is installed — see
`01-constraints.md`.

---

## Baseline: the built-in `Navigator`

- **`Navigator.push`/`pop` with `MaterialPageRoute`** (or named
  routes via `onGenerateRoute` in `app.dart`) is the navigation
  model until a real need — deep linking, nested/tabbed navigation
  stacks the imperative API can't express cleanly — justifies a
  routing package.
- **Adopting a routing package (e.g. `go_router`) is an
  architectural decision.** It requires an ADR, per
  `01-constraints.md`.

## Named routes, if used

- **Route name constants are declared in one place** (e.g.
  `app.dart` or a small `routes.dart`), not as string literals
  scattered across call sites.
- Route names are `PascalCase` and match the screen they open:
  `ProfileDetailScreen` → route `/profile-detail` or
  `ProfileDetail`, consistently.

## Back behaviour

- **Rely on the platform default** (`AppBar` back button, Android
  system back popping the stack) unless a screen holds unsaved
  state — guard those with `PopScope` (or `WillPopScope` on older
  SDKs) rather than leaving data loss silent.

## Modals and sheets

- Use `showDialog`/`showModalBottomSheet` from the framework. Do
  not hand-roll a modal with a conditionally-rendered overlay
  widget.
