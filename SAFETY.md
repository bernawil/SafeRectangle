# SafeRectangle Safety Notes

SafeRectangle is intended to be a quiet local utility. The fork removes remote
update and external command surfaces that are convenient in a general-purpose
distribution but unnecessary for a local-only window manager.

## Removed Remote Capabilities

- Sparkle framework and package dependency
- Sparkle updater helper apps, XPC services, and appcast metadata
- `SUFeedURL`, `SUPublicEDKey`, and scheduled update settings
- "check for updates" behavior
- `rectangle://` URL scheme registration
- external URL command handling
- remote SwiftPM dependency resolution for MASShortcut

## Dependency Model

MASShortcut is vendored in `Vendor/MASShortcut`. This keeps shortcut recording
source local to the repository and avoids fetching package source while building.

## Verification Targets

Before publishing a release build, check:

```sh
rg -n 'Sparkle|SPU|SUFeedURL|SUPublicEDKey|SUScheduledCheckInterval|URLSession|NSURLConnection|CFNetwork|NWConnection|http://|https://' \
  Rectangle RectangleLauncher Vendor --glob '!**/*.xcstrings'

strings -a build/SafeRectangleDerivedData/Build/Products/Release/SafeRectangle.app/Contents/MacOS/SafeRectangle | \
  rg 'Sparkle|URLSession|NSURLConnection|CFNetwork|NWConnection|http://|https://' || true

codesign --verify --deep --strict --verbose=4 \
  build/SafeRectangleDerivedData/Build/Products/Release/SafeRectangle.app
```

XML plist DTDs and SVG namespace strings can contain URL-looking text. Those are
file-format identifiers, not network endpoints.

## Remaining Permission

SafeRectangle requires Accessibility permission because macOS window management
uses Accessibility APIs. That permission lets the app inspect and move windows.
It is expected for this kind of tool.

The app intentionally does not include network code. macOS does not enforce a
network entitlement ban for the unsandboxed main app, so this is a source and
binary hardening decision rather than an OS-level sandbox guarantee.
