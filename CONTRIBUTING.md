# Contributing

SafeRectangle is a local-only fork focused on keeping Rectangle-style window
management without updater, telemetry, external URL command, or remote package
fetch behavior.

## Project Priorities

- Keep the app local and quiet.
- Do not add telemetry, analytics, crash-report uploaders, or remote update
  frameworks.
- Do not add URL schemes or external command handlers.
- Keep MASShortcut vendored unless there is a deliberate review of a
  replacement.
- Preserve the MIT license and upstream attribution.

## Bugs

Please include:

- SafeRectangle version/build
- macOS version
- whether Accessibility permission is enabled
- the exact shortcut or window action involved
- screenshots or screen recordings if they clarify the issue

## Code Style

Match the existing style and keep changes narrowly scoped. Internal source
symbols may still use Rectangle names where they are not user-facing; visible
app text and documentation should use SafeRectangle.

## License

By contributing to SafeRectangle you agree that your contributions are licensed
under the MIT license used by this repository.
