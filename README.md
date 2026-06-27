# SafeRectangle

SafeRectangle is a local-only fork of Rectangle, the open source macOS window
manager based on Spectacle. The goal is to keep the familiar keyboard shortcuts,
snap areas, and lightweight menu-bar workflow while removing updater, telemetry,
external URL, and build-time remote dependency paths.

Project home: https://github.com/bernawil/SafeRectangle

## What Changed

- Renamed the application and user-facing labels from Rectangle to SafeRectangle.
- Changed the bundle identifiers to `com.bernawil.SafeRectangle` and
  `com.bernawil.SafeRectangleLauncher`.
- Removed Sparkle.
- Removed update feed metadata and update menu behavior.
- Removed the `rectangle://` URL scheme and external URL command handler.
- Removed the old `InternetAccessPolicy.plist`.
- Removed Sparkle-related user-default keys and update notifications.
- Vendored MASShortcut in `Vendor/MASShortcut` so the project builds from local
  source instead of fetching a Swift Package Manager dependency from GitHub.
- Disabled update UI controls. The app does not check for updates.

## What Sparkle Is

Sparkle is a popular macOS app update framework. It can check a remote appcast,
download an update, validate it, and install it. That is useful for normal app
distribution, but it is also a remote update channel. SafeRectangle removes
Sparkle completely so this fork has no built-in updater.

## Local-Only Scope

SafeRectangle contains no intentional internet, telemetry, or remote update
behavior. Source and binary checks are intended to stay clean for:

- Sparkle updater symbols and bundles
- update-feed keys such as `SUFeedURL`
- network client APIs such as `URLSession`, `NSURLConnection`, `CFNetwork`, and
  `NWConnection`
- telemetry service names
- remote package pins

SafeRectangle still needs macOS Accessibility permission to move and resize
windows. The main app is intentionally not sandboxed because macOS window
management through Accessibility does not work well under a strict app sandbox.
That means this fork removes network code; it does not rely on an operating
system network entitlement to enforce a hard network ban.

## Build

```sh
xcodebuild \
  -project Rectangle.xcodeproj \
  -scheme Rectangle \
  -configuration Release \
  -derivedDataPath build/SafeRectangleDerivedData \
  CODE_SIGN_IDENTITY=- \
  CODE_SIGN_STYLE=Manual \
  DEVELOPMENT_TEAM= \
  ASSETCATALOG_OTHER_FLAGS= \
  build
```

The app is produced at:

```text
build/SafeRectangleDerivedData/Build/Products/Release/SafeRectangle.app
```

For local hardened ad-hoc signing after the build:

```sh
codesign --force --sign - --options runtime --timestamp=none \
  --entitlements RectangleLauncher/RectangleLauncherRelease.entitlements \
  build/SafeRectangleDerivedData/Build/Products/Release/SafeRectangle.app/Contents/Library/LoginItems/SafeRectangleLauncher.app

codesign --force --sign - --options runtime --timestamp=none \
  --entitlements Rectangle/RectangleRelease.entitlements \
  build/SafeRectangleDerivedData/Build/Products/Release/SafeRectangle.app
```

## Install

```sh
ditto build/SafeRectangleDerivedData/Build/Products/Release/SafeRectangle.app /Applications/SafeRectangle.app
xattr -dr com.apple.quarantine /Applications/SafeRectangle.app 2>/dev/null || true
open /Applications/SafeRectangle.app
```

Then enable SafeRectangle in:

```text
System Settings -> Privacy & Security -> Accessibility
```

## Configuration

SafeRectangle stores preferences under:

```text
~/Library/Preferences/com.bernawil.SafeRectangle.plist
```

On launch it can import:

```text
~/Library/Application Support/SafeRectangle/SafeRectangleConfig.json
```

After import, the file is renamed so it is not repeatedly applied.

## Testing

```sh
xcodebuild test \
  -project Rectangle.xcodeproj \
  -scheme Rectangle \
  -configuration Debug \
  -derivedDataPath build/SafeRectangleTestDerivedData \
  CODE_SIGN_IDENTITY=- \
  CODE_SIGN_STYLE=Manual \
  DEVELOPMENT_TEAM= \
  ASSETCATALOG_OTHER_FLAGS= \
  -destination 'platform=macOS,arch=arm64'
```

## Attribution

SafeRectangle is a fork of Rectangle by Ryan Hanson. Rectangle is based on
Spectacle. SafeRectangle keeps the original MIT license and copyright notices.
