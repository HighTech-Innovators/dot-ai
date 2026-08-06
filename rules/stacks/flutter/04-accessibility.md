# Flutter Accessibility

Accessibility is a correctness requirement, not a polish pass. A
screen that doesn't work with TalkBack/VoiceOver is not done.

---

## Labels and semantics

- **Icon-only controls declare a label** — `IconButton(tooltip: …)`,
  `Icon(semanticLabel: …)`, or a wrapping `Semantics(label: …)`.
  An icon with no visible text and no label is invisible to
  assistive tech.
- **State is announced, not just shown.** Use `Semantics(selected:
  …, checked: …, enabled: …)` (or the equivalent built into the
  Material widget you're using) so toggles and selections are
  exposed to screen readers, not conveyed by color alone.

## Touch targets and text

- **Tap targets meet the platform minimum** — 48×48 logical pixels
  on Android, 44×44 on iOS. Don't shrink an `IconButton` or
  `GestureDetector` below that to fit a layout.
- **Text respects the system text scale.** Do not hardcode a fixed
  `TextScaler`/`textScaleFactor` override without a recorded
  reason — users who increase system font size expect this app to
  follow.
- **Color is never the only signal** for error, selected, or
  disabled state — pair it with an icon, text, or shape change.

## Testing accessibility

- **Every new screen is walked with TalkBack (Android) or
  VoiceOver (iOS)** before it's considered done — this is a step,
  not a nicety.
- **Widget tests prefer semantics-based finders**
  (`find.bySemanticsLabel`) over `find.byKey` where possible — see
  `02-testing.md`. If a widget can't be found by its semantics, it
  probably isn't accessible.
