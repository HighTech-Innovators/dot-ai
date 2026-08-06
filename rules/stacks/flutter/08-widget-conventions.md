# Flutter Widget Conventions

How individual widgets are authored and split. Complements
`05-folder-structure.md` (between-widget organisation) and
`09-architecture.md` (why logic belongs on the ViewModel, not the
widget — this file is about splitting the *View*, not where
business logic lives).

---

## Stateless by default

- **Prefer `StatelessWidget`.** Reach for `StatefulWidget` only
  when the widget owns mutable state that doesn't belong in a
  shared store or an ancestor.
- **`const` constructors on every widget whose fields allow it**,
  per `01-constraints.md`.

## Splitting widgets

- **Split a `build()` method once it exceeds ~150 lines**, or once
  it's doing more than one visually distinct thing. Extract
  private widget classes (prefixed `_`, e.g. `_ProfileAvatar`) —
  not helper methods that still return a `Widget`. A method that
  returns a `Widget` doesn't get its own `const`-ness or its own
  `BuildContext` scoping; a class does.
- **One public widget per file** for anything beyond a few lines.
  Trivial, single-leaf widgets can stay as a private class in the
  same file as their only caller.

## Keys

- **Give a widget a `Key` only when list identity matters** — an
  item in a list that can reorder, be inserted, or be removed.
  Don't add keys speculatively to widgets that don't need them.

## Constructors

- **Named parameters for any constructor with more than one or two
  fields.** `const UserCard({required this.name, required
  this.avatarUrl})`, not positional.
- **`required` on every non-nullable field without a default.**
  Don't paper over a missing value with a fallback inside the
  widget when the caller should have supplied it.
