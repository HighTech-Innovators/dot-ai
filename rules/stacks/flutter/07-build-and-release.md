# Flutter Build and Release

How the app is built and versioned. Expand this file once CI and
signing are actually set up for the project — do not invent
process that doesn't exist yet.

---

## Build system

- **Platform folders (`android/`, `ios/`, `linux/`, `macos/`,
  `web/`, `windows/`) are committed** and reviewed like any other
  code, per `01-constraints.md`. There is no regenerate-from-config
  step.
- **If no CI pipeline exists yet**, `flutter build <platform>` run
  locally is the only build path. Setting up CI is an
  architect-level decision, not something to add incidentally
  inside a feature PR.

## Versioning

- **App version and build number both come from the single
  `version:` field in `pubspec.yaml`** (`1.0.0+1` → version name
  `1.0.0`, build number `1`). The Flutter tool derives the
  platform-specific version fields from this at build time — don't
  hand-edit `Info.plist`/`build.gradle` version fields separately.
- **Version bumps follow semver** and are their own deliberate
  change, per `global/01-principles.md`.

## Native changes

- **Edits to `android/**/build.gradle`, `ios/Runner.xcodeproj`,
  platform manifests, or `Info.plist` are called out explicitly**
  in the PR description with the reason, even without CI to catch
  a mismatch.

## Signing and credentials

- **Do not commit keystores, `.jks`/`.p12` files, or provisioning
  profiles** — see `global/08-secrets-and-data.md`. Setting up
  signing is a build-and-release decision for the architect, not
  something to improvise per release.
