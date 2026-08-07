# Flutter Constraints

Hard rules for Flutter/Dart code in this project. If a constraint
here conflicts with what you're about to write, stop and escalate —
don't route around it.

---

## Language and framework baseline

- **Null safety is mandatory.** The SDK constraint in `pubspec.yaml`
  already requires it — no `!` used to silence a null-safety
  concern you haven't actually ruled out.
- **Material 3 is the design baseline** (`useMaterial3: true`).
  Do not revert to Material 2 widgets or theming.
- **`flutter_lints` (via `analysis_options.yaml`) is the enforced
  lint set.** Do not disable a rule project-wide to silence a
  warning — use a scoped `// ignore:` with a reason, or fix the
  code.
- **`const` constructors wherever the arguments allow it.** This
  is enforced by `prefer_const_constructors` in the lint set —
  treat a lint failure here as a bug, not noise to suppress.

## Default architecture stack

This project follows Flutter's own architecture recommendation
(MVVM + repository pattern — see `09-architecture.md`), which pulls
in two packages by default:

- **[`provider`](https://pub.dev/packages/provider) is the default
  dependency-injection mechanism.** Add it to `pubspec.yaml` if
  it's missing — no ADR needed for `provider` specifically.
- **[`go_router`](https://pub.dev/packages/go_router) is the
  default navigation package** (see `06-navigation.md`) — no ADR
  needed for `go_router` specifically.

## No other presumed third-party stack

- **Do not assume any other state-management package** (Riverpod,
  Bloc, GetX, or similar) is installed. Check `pubspec.yaml` first.
- **Adopting one of those is still an architectural decision.**
  Escalate to the architect and record the choice in an ADR before
  adding it — per `global/01-principles.md` ("new dependencies
  without a recorded reason").
- **`ChangeNotifier`/`Listenable` (bundled with the SDK) are the
  default way to expose ViewModel state to widgets** until/unless
  the project adopts something else via that ADR — see
  `09-architecture.md`.

## Platform folders

- **`android/`, `ios/`, `linux/`, `macos/`, `web/`, `windows/` are
  committed** and reviewed like any other code — there is no
  regenerate-from-config step. See `07-build-and-release.md`.

## Things that are out of scope for this file

Testing (`02-testing.md`), performance (`03-performance.md`),
accessibility (`04-accessibility.md`), folder structure
(`05-folder-structure.md`), navigation (`06-navigation.md`),
build/release (`07-build-and-release.md`), widget-authoring
conventions (`08-widget-conventions.md`), and the UI/data
architecture layering (`09-architecture.md`) each live in their
own file.
