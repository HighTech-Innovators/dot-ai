# Flutter Folder Structure

Feature-first organisation under `lib/`. Extends the baseline in
`01-constraints.md`.

---

## Layout

```text
lib/
├── main.dart          # Entry point only — construct and run the root widget
├── app.dart           # Root widget: MaterialApp, theme, top-level routing
├── core/              # Infrastructure the app cannot run without
│   └── theme/         # ColorScheme, TextTheme, spacing constants
├── features/          # Feature modules (e.g. auth, profile, checkout)
│   └── <feature>/
│       ├── screens/   # Full-screen widgets for this feature
│       ├── widgets/   # Feature-specific, non-shared widgets
│       └── models/    # Feature-specific data classes
└── common/            # Generic, feature-agnostic widgets and utilities

test/                  # Mirrors lib/ path-for-path — see 02-testing.md
```

## `core/` vs `common/` vs `features/`

- **`core/`** is infrastructure — theming, app-wide services. The
  app does not start without it. Changes here affect every
  feature and are reviewed accordingly.
- **`common/`** is generic reuse — a widget or utility that
  references a specific feature does not belong here.
- **`features/<name>/`** owns everything specific to that feature.
  When code in one feature is needed by a second, it graduates to
  `common/` (UI/util) or `core/` (infrastructure) — features do
  not import each other's internals.

## Screens

- Feature screens live in `features/<feature>/screens/` and are
  named `<Name>Screen`.

## What does not live where

- **No screens in `common/`.** A widget that owns a full screen is
  a screen, and screens belong to a feature.
- **No I/O in `common/` utilities.** Utilities are pure; anything
  touching network or storage belongs in a feature or in `core/`
  once that boundary is established.
