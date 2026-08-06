# Flutter Navigation

How screens are wired. `go_router` is the default navigation
package for this project — see `01-constraints.md`.

---

## Baseline: `go_router`

- **[`go_router`](https://pub.dev/packages/go_router) is the
  default navigation solution**, per Flutter's own architecture
  recommendation — it covers the large majority of navigation
  needs (declarative routes, deep linking, nested/tabbed
  navigation) that the imperative `Navigator` API can't express
  cleanly.
- **Routes are declared in one place** (e.g.
  `routing/app_router.dart`), not as `GoRoute` literals or path
  strings scattered across call sites.
- Route names are `PascalCase` and match the screen they open:
  `ProfileDetailScreen` → route name `ProfileDetail`, path
  `/profile-detail`, consistently.

## Falling back to the built-in `Navigator`

- **The imperative `Navigator` API is still available** for the
  minority of cases `go_router` doesn't solve cleanly — e.g. a
  transient overlay pushed from deep inside a widget tree. Reach
  for it explicitly for those cases; don't route around `go_router`
  by habit.
- **Adopting a different routing package instead of `go_router`
  is an architectural decision.** It requires an ADR, per
  `01-constraints.md`.

## Back behaviour

- **Rely on the platform default** (`AppBar` back button, Android
  system back popping the stack) unless a screen holds unsaved
  state — guard those with `PopScope` (or `WillPopScope` on older
  SDKs) rather than leaving data loss silent.

## Modals and sheets

- Use `showDialog`/`showModalBottomSheet` from the framework. Do
  not hand-roll a modal with a conditionally-rendered overlay
  widget.
