# Flutter Folder Structure

Feature-first organisation under `lib/`, split along the UI/data
layering from `09-architecture.md`. Extends the baseline in
`01-constraints.md`.

---

## Layout

```text
lib/
├── main.dart            # Entry point only — construct and run the root widget
├── app.dart             # Root widget: MaterialApp.router, top-level theme
├── routing/             # go_router configuration — see 06-navigation.md
│   └── app_router.dart
├── ui/
│   ├── core/            # Shared, feature-agnostic widgets and theming
│   │   ├── theme/       # ColorScheme, TextTheme, spacing constants
│   │   └── widgets/     # Shared widgets used across features
│   └── <feature>/       # Feature UI (e.g. auth, profile, checkout)
│       ├── view_model/  # ViewModel classes — see 09-architecture.md
│       ├── widgets/     # Feature-specific, non-shared widgets
│       └── <feature>_screen.dart
├── domain/              # Optional — models/use-cases, only once logic
│   └── models/          # crowds ViewModels — see 09-architecture.md
└── data/
    ├── repositories/    # Abstract repository classes + implementations
    └── services/        # Wraps a single data source (API client, DB, ...)

test/                    # Mirrors lib/ path-for-path — see 02-testing.md
```

## `ui/core/` vs `data/` vs `ui/<feature>/`

- **`ui/core/`** is shared, feature-agnostic UI — theming, widgets
  used by more than one feature. Changes here affect every feature
  and are reviewed accordingly. Don't name this folder `widgets/`
  on its own — that name collides with Flutter SDK vocabulary once
  the project has more than a couple of shared widgets.
- **`data/`** is the data layer — repositories and services. A
  repository or service that reaches into a specific feature's UI
  code does not belong here; data flows one way (see
  `09-architecture.md`).
- **`ui/<feature>/`** owns everything specific to that feature's UI
  (screens, ViewModels, feature-specific widgets). When code in one
  feature is needed by a second, it graduates to `ui/core/` (UI) or
  `data/` (repository/service) — features do not import each
  other's internals.
- **`domain/`** is optional. Add it only once application logic is
  complex enough to crowd ViewModels or gets repeated across them
  — see `09-architecture.md`. Don't create it speculatively.

## Screens and ViewModels

- Feature screens live in `ui/<feature>/` and are named
  `<Name>Screen`.
- Feature ViewModels live in `ui/<feature>/view_model/` and are
  named `<Name>ViewModel`, one per screen unless screens genuinely
  share state.

## What does not live where

- **No screens in `ui/core/`.** A widget that owns a full screen is
  a screen, and screens belong to a feature.
- **No I/O in `ui/core/` widgets.** Widgets are pure UI; anything
  touching network or storage belongs in `data/` and is read by a
  ViewModel, not called directly from a widget.
