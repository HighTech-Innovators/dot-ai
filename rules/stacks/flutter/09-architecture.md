# Flutter Architecture

Layering rules for how UI, business logic, and data fit together —
Flutter's own MVVM + repository recommendation, adapted to this
project. The resulting folder layout lives in
`05-folder-structure.md`; the packages this pulls in are declared
default in `01-constraints.md`.

Priority follows Flutter's own recommendation levels: a "strongly
recommend" item is the default for new code; "recommend" applies
unless there's a specific reason not to; "conditional" applies only
once its trigger condition is actually met — don't adopt it
speculatively.

---

## Separation of concerns

- **Separate the app into a UI layer and a data layer.** (Strongly
  recommend.) The data layer exposes application data and owns
  most business logic. The UI layer displays that data and reports
  user events back to it.
- **Use the repository pattern in the data layer.** (Strongly
  recommend.) `Repository` classes isolate data-access logic
  (network, database, file system) behind an abstraction; `Service`
  classes each wrap a single data source (an API client, a local
  DB). Repositories are abstract so a different implementation can
  back "development" vs "staging" environments.
- **Use ViewModels and Views in the UI layer (MVVM).** (Strongly
  recommend.) A View (a `Screen` widget) is "dumb" — it renders
  state and forwards user events. A ViewModel holds that state and
  the logic that produces it. This is what keeps widgets small and
  testable per `08-widget-conventions.md`.
- **Do not put logic in widgets.** (Strongly recommend.) Logic
  belongs on the ViewModel. The only logic a View may contain:
  - Simple `if`-statements showing/hiding widgets based on a flag
    or nullable ViewModel field.
  - Animation logic the widget needs to calculate.
  - Layout logic based on device info (screen size, orientation).
  - Simple routing logic.
- **Expose ViewModel state to widgets with `ChangeNotifier`/
  `Listenable`.** (Conditional — the default until/unless the
  project adopts a different state-management package via ADR, per
  `01-constraints.md`.) `ChangeNotifier` is bundled with the SDK;
  widgets observe it via `provider` (`ChangeNotifierProvider` +
  `context.watch`/`Consumer`) or `ListenableBuilder` directly.
- **Use a domain layer only if it earns its keep.** (Conditional.)
  A domain layer (use-cases) is worth adding once application logic
  is complex enough to crowd ViewModels, or the same logic repeats
  across multiple ViewModels. In most apps it's unnecessary
  overhead — don't add one speculatively.

## Handling data

- **Use unidirectional data flow.** (Strongly recommend.) Data
  updates flow data layer → UI layer only. UI interactions are sent
  to the data layer, which processes them and produces new state.
- **Use immutable data models.** (Strongly recommend.) A model that
  can't be mutated after construction can't be corrupted by a
  stray write from the wrong layer — a change means constructing a
  new instance, not editing one in place.
- **Use `Command`s to handle user-interaction events.** (Recommend.)
  A `Command` wraps a ViewModel action (loading state, error
  handling, guarding against double-invocation) so the View doesn't
  have to reimplement that plumbing at every button press.
- **Consider `freezed` or `built_value` for immutable models.**
  (Recommend, conditional on model count.) These generate
  `copyWith`, deep equality, and JSON ser/des. They add build time —
  worth it once the app has more than a handful of models, not
  before.
- **Create separate API models and domain models only in large
  apps.** (Conditional.) Splitting adds verbosity but keeps
  ViewModels/use-cases from absorbing API-shape churn. Don't split
  by default for a small app.

## Dependency injection and navigation

- **`provider` is the default dependency-injection mechanism**, and
  **`go_router` is the default navigation package** — both
  installed by default rather than escalated per-project. See
  `01-constraints.md` and `06-navigation.md`.

## Naming and location

- **Name classes for the architectural component they represent**:
  `HomeViewModel`, `HomeScreen`, `UserRepository`,
  `ClientApiService`. Don't give a class a generic name that hides
  which layer it belongs to.
- **Shared, feature-agnostic widgets live in `ui/core/`**, not a
  folder called `widgets/` — see `05-folder-structure.md`. Avoid
  naming anything so it could be confused with a Flutter SDK class.

## Testing per layer

- **Unit test every service, repository, and ViewModel class**,
  testing each method's logic individually.
- **Widget-test every View**, with particular attention to routing
  and dependency-injection wiring — not just that it renders.
- **Prefer hand-written fakes over mock packages.** A fake models
  inputs/outputs rather than internal call expectations, and
  writing one forces the production code to have well-defined
  inputs and outputs. The `mockito`/`mocktail` restriction in
  `02-testing.md` still applies — fakes don't need a package.
