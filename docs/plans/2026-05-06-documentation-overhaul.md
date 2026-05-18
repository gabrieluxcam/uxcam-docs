---
hidden: true
---
# UXCam Documentation Overhaul — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Fix deprecated API references, add Android 3.10.0 + iOS 3.8.1 content, update changelogs, and improve quality/depth of React Native, Flutter, iOS, and cross-platform docs.

**Architecture:** Priority-driven approach — fix dangerous deprecated code examples first (what customers copy-paste), then add missing SDK version content, then improve thin/missing documentation pages. Each task is a logical commit.

**Branch:** `v0.0.1-github` (working branch) → PR to `v1.1` (main)

---

## Phase 1: Changelog Updates (Android 3.10.0, 3.9.0, 3.8.14, iOS 3.8.1)

### Task 1: Update Android Changelog with 3.10.0, 3.9.0, and 3.8.14

**Files:**
- Modify: `docs/CHANGE LOG/android-changelog.md` (add entries at top)

**Step 1: Add Android 3.10.0 entry**

Insert after the `# UXCam Android Changelog` heading and `<br />`, BEFORE the 3.9.1 entry:

```markdown
### V. 3.10.0 - May 5, 2026

* **New**: Readable fragment screen tagging — fragment screens now show as `Activity/Fragment` (e.g. `MainActivity/HomeFragment`) instead of hex hashes (e.g. `MainActivity/0xa1b2c3`) in the dashboard timeline
* **New**: Opt-out available via `UXConfig.Builder.enableReadableFragmentNames(false)` for customers with analytics tied to legacy hash names
* **Note**: Existing occlusion rules using old hex hash names continue to work — the SDK matches against both names
* Feat (frame-sync): marker-based occlusion via `FrameSyncManager`
* Fix (occlusion): evict detached Compose occlusions on read
* Fix (capture): skip captures when activity view tree isn't laid out yet
* Fix (occlusion): key `autoByRoot` by `View` to prevent silent bucket loss
* Fix (logcat): use `ProcessBuilder` to process logcat data across all API levels

<br />
```

**Step 2: Replace the incomplete 3.9.0 entry**

Replace:
```markdown
### V. 3.9.0 - Mar 2026

* Interim release (bundled by React Native 6.0.15). Detailed notes pending.
```

With:
```markdown
### V. 3.9.0 - Mar 31, 2026

* Feat (identity): user identity is now in-memory only, decoupled from session restart
* Fix: simplified deferred startup lifecycle handling
* Fix (screenshot): centralized blur backend routing
* Fix: redact screen action details for occluded `EditText` views
* Fix: allow gesture tracking on auto-occluded `EditText` views
* Fix (Cordova): text fields get automatically occluded
* Fix: add support for blur radius greater than 25px
```

**Step 3: Add 3.8.14 entry**

Insert between the 3.9.0 and 3.8.13 entries:

```markdown
<br />

### V. 3.8.14 - Mar 13, 2026

* Fix (response): prevent NPE when null data is received from server
* Fix: session cancelling due to `OutOfMemoryException`
* Fix: allow per-view occlusion to apply alongside full-screen occlusion
```

**Step 4: Commit**

```
docs(changelog): add Android 3.10.0, expand 3.9.0, add 3.8.14
```

---

### Task 2: Update iOS Changelog with 3.8.1

**Files:**
- Modify: `docs/CHANGE LOG/ios-changelog.md` (add entry at top)

**Step 1: Add iOS 3.8.1 entry**

Insert after the `# UXCam iOS Changelog` heading, BEFORE the 3.8.0 entry:

```markdown
### V. 3.8.1 - May 2026

* Optimized video recording flow — major internal refactoring of video pipeline architecture for improved performance and stability
* Added missing public headers: `UXCamOcclusionParameter` and `UXCamOcclusionScreen`
* Fix: video duration drift under backpressure
* Fix: sensitive animation occlusion handling for modal/popover presentation and dismissal
* Fix: image rotation issue for image-only mode
* Fix: Flutter late-attach and capture pipeline improvements
```

**Step 2: Expand 3.7.12 entry**

Replace:
```markdown
### V. 3.7.12 - Apr 1, 2026

* No changelog published for this release.
```

With:
```markdown
### V. 3.7.12 - Apr 1, 2026

* Fixed crash related to `NSFileHandleOperationException`, `NSInvalidArgumentException`, internal logging, `setupDisplayLink`, and status bar orientation
* Updated occlusion metadata
```

**Step 3: Commit**

```
docs(changelog): add iOS 3.8.1, expand 3.7.12
```

---

## Phase 2: Deprecated API Fixes Across All Platforms

### Task 3: Fix `optIntoSchematicRecordings()` in iOS docs

**Files:**
- Modify: `docs/Mobile SDK Installation/ios/index.md`
- Modify: `docs/Mobile SDK Installation/ios/swiftui-ios.md`
- Modify: `docs/Mobile SDK Installation/ios/screen-tagging/automatic-screen-tagging-swiftui-ios.md`

**Step 1: Fix iOS main index.md**

In `ios/index.md`, replace line 133:
```swift
    UXCam.optIntoSchematicRecordings()
```
With:
```swift
    UXCam.optIntoVideoRecordings()
```

Also fix the JumpCallout anchor on line 15:
```
<JumpCallout to="#android-sdk--quickstart">Jump to Quickstart</JumpCallout>
```
→
```
<JumpCallout to="#quick-start-only-a-couple-of-lines-of-code">Jump to Quickstart</JumpCallout>
```

**Step 2: Fix swiftui-ios.md**

Search for `optIntoSchematicRecordings` and replace with `optIntoVideoRecordings`.

**Step 3: Fix automatic-screen-tagging-swiftui-ios.md**

Search for `optIntoSchematicRecordings` and replace with `optIntoVideoRecordings`. Also replace `UXCamCore.optIntoSchematicRecordings()` with `UXCamCore.optIntoVideoRecordings()` if present.

**Step 4: Fix deprecated `disableCrashHandling` in crashes-anrs-copy-ios.md**

In `ios/advanced-configuration-and-apis-1/crashes-anrs-copy-ios.md`, replace:
```swift
UXCam.disableCrashHandling(true)
```
With a note:
```swift
// Using UXCamConfiguration (recommended)
let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")
config.enableCrashHandling = false // Disabled (enabled by default)
UXCam.start(with: config)
```

**Step 5: Commit**

```
docs(ios): replace deprecated optIntoSchematicRecordings with optIntoVideoRecordings
```

---

### Task 4: Fix `optIntoSchematicRecordings()` in React Native docs

**Files:**
- Modify: `docs/Mobile SDK Installation/react-native/index.md`
- Modify: `docs/Mobile SDK Installation/react-native/set-up-the-sdk-and-start-recording-react-native.md`
- Modify: `docs/Mobile SDK Installation/react-native/troubleshooting-react-native-uxcam-react-native.md`

**Step 1: In each file, replace ALL occurrences of:**
```javascript
RNUxcam.optIntoSchematicRecordings();
```
With:
```javascript
RNUxcam.optIntoVideoRecordings(); // Enable video recording on iOS
```

And replace any standalone:
```javascript
UXCam.optIntoSchematicRecordings();
```
With:
```javascript
UXCam.optIntoVideoRecordings(); // Enable video recording on iOS
```

**Step 2: Also replace any `enableImprovedScreenCapture: true` in configuration objects**

Add a comment noting it's no longer needed:
```javascript
// enableImprovedScreenCapture is no longer needed — improved capture is always on
```

Or simply remove the line from examples if it won't break the example.

**Step 3: Commit**

```
docs(react-native): replace deprecated schematic recording API calls
```

---

### Task 5: Fix `optIntoSchematicRecordings()` in Flutter docs

**Files:**
- Modify: `docs/Mobile SDK Installation/flutter/index.md`
- Modify: `docs/Mobile SDK Installation/flutter/advanced-configuration-and-apis/index.md`
- Modify: `docs/Mobile SDK Installation/flutter/advanced-configuration-and-apis/opt-in-opt-out-flutter.md`
- Modify: `docs/Mobile SDK Installation/flutter/troubleshooting-flutter.md`

**Step 1: In each file, replace ALL occurrences of:**
```dart
FlutterUxcam.optIntoSchematicRecordings();
```
With:
```dart
FlutterUxcam.optIntoVideoRecordings(); // Enable video recording on iOS
```

**Step 2: Commit**

```
docs(flutter): replace deprecated schematic recording API calls
```

---

### Task 6: Fix deprecated APIs in Cordova, NativeScript, and Xamarin docs

**Files:**
- Modify: `docs/Mobile SDK Installation/cordova/index.md`
- Modify: `docs/Mobile SDK Installation/nativescript/index.md`
- Modify: `docs/Mobile SDK Installation/xamarin-maui/index.md`

**Step 1: Cordova — replace all `optIntoSchematicRecordings()` references**

In `cordova/index.md`, replace every:
```javascript
UXCam.optIntoSchematicRecordings(); // Enable session video recording on iOS
```
With:
```javascript
UXCam.optIntoVideoRecordings(); // Enable video recording on iOS
```

Also remove `enableImprovedScreenCapture: true` from configuration objects (no longer needed).

**Step 2: NativeScript — same replacement**

In `nativescript/index.md`, replace every:
```typescript
NSUXCam.optIntoSchematicRecordings(); // Enable iOS screen recordings
```
With:
```typescript
NSUXCam.optIntoVideoRecordings(); // Enable video recording on iOS
```

**Step 3: Xamarin MAUI — replace and fix typo**

In `xamarin-maui/index.md`:

a) Replace all `UXCam.OptIntoSchematicRecordings()` with `UXCam.OptIntoVideoRecordings()`

b) Fix the typo on line 47:
```
## SDK Installation - Xmarin Deprecated Verdsion
```
→
```
## SDK Installation — Xamarin (Deprecated)
```

c) Remove `enableImprovedScreenCapture: true` from config examples.

d) Replace the deprecated old help center link at the bottom:
```markdown
> To stay up to date with the latest SDK improvements, visit [this link](https://help.uxcam.com/hc/en-us/articles/4404509626509--SDK-UPDATES).
```
→
```markdown
> To stay up to date with the latest SDK improvements, visit [our changelog](https://developer.uxcam.com/docs/xamarin-changelog).
```

**Step 4: Commit**

```
docs(cross-platform): fix deprecated APIs in Cordova, NativeScript, Xamarin
```

---

## Phase 3: Android 3.10.0 — Readable Fragment Screen Tagging Documentation

### Task 7: Rewrite Fragment-Based Tagging Page for 3.10.0

**Files:**
- Modify: `docs/Mobile SDK Installation/android/screen-tagging/fragment-based-tagging-android.md`

**Step 1: Replace the entire file content (currently 45 thin lines) with comprehensive documentation**

The new content should cover (source: Google Doc + SDK analysis):

```markdown
---
title: Fragment-Based Screen Tagging
excerpt: 'How fragment screens appear in the UXCam timeline'
deprecated: false
hidden: false
metadata:
  title: Fragment-Based Screen Tagging
  description: >-
    How UXCam tags fragment screens in Android apps, including the readable
    fragment name feature introduced in SDK v3.10.0.
  robots: index
next:
  description: ''
---

# Fragment-Based Screen Tagging

Fragment-based tagging automatically tags each visible Fragment in your app so it appears as a named screen in the UXCam timeline. This is enabled from your **dashboard** — no code changes required.

## Enabling Fragment Tagging

Navigate to **App Settings → Data capture & recording → Additional settings** and enable **"Auto tag screen fragments on Android"**.

<Image align="center" width="80%" src="https://files.readme.io/d5659a9-FragmentTagging.png" />

> 📘 **Requires AndroidX.** Fragment-based tagging uses the AndroidX Fragment lifecycle. Classic `android.app.Fragment` is not supported.

---

## Readable Fragment Names (SDK v3.10.0+)

Starting with Android SDK **v3.10.0**, fragment screen tags show **human-readable names** instead of hex hashes.

### Before (SDK ≤ 3.9.x)

```
→ MainActivity
→ MainActivity/0x4f3a8b
→ MainActivity/0xc91e2d
```

Customers had to manually look up which Fragment a hash referred to.

### After (SDK 3.10.0+, default on)

```
→ MainActivity
→ MainActivity/HomeFragment
→ MainActivity/SettingsFragment
```

Names match the actual `Fragment` class in your app.

### How the SDK Picks the Fragment Name

The SDK uses a **dominant fragment** rule. When multiple fragments are visible, it picks the one that covers **≥ 50% of the screen**.

| Scenario | Result |
|----------|--------|
| Single fragment full-screen | Tag is that fragment: `MainActivity/HomeFragment` |
| Multi-pane / master-detail (40%/60%) | Tag is the larger fragment: `MainActivity/DetailFragment` |
| Small overlay fragment (< 50% of screen) | Tag is the activity: `MainActivity` |
| Dialog over fragment → dialog dismisses | Tag returns to the underlying fragment |

### Backward Compatibility with Occlusion Rules

Occlusion rules (set via dashboard or `UXCam.applyOcclusion(...)`) match against screen names. **Existing rules using old hex hash names continue to work.** The SDK computes the legacy hash alongside the readable name and matches against both.

- A rule `"occlude MainActivity/0xc91e2d"` still triggers even though the timeline now shows `MainActivity/SettingsFragment`
- You can update rules to readable names at your convenience — no rush, no breakage

### Opting Out (Legacy Behavior)

If your analytics or funnels depend on the old hash names, you can opt out:

```kotlin Kotlin
val config = UXConfig.Builder("YOUR_APP_KEY")
    .enableReadableFragmentNames(false)
    .build()
UXCam.startWithConfiguration(config)
```

```java Java
UXConfig config = new UXConfig.Builder("YOUR_APP_KEY")
    .enableReadableFragmentNames(false)
    .build();
UXCam.startWithConfiguration(config);
```

With opt-out, the timeline shows `MainActivity/0xa1b2c3` as before, matching v3.9.x behavior exactly.

---

## Recommendations

> 👍 **Best Practice**
>
> If you enable fragment tagging, we recommend using **only automatic screen name tagging** (`enableAutomaticScreenNameTagging = true`) to avoid mismatches between manually tagged and automatically tagged screens.

> ⚠️ **If you use manual tagging alongside fragment tagging**, your manual tags will take precedence for the screens you tag, while the SDK will auto-tag the rest. Make sure your manual names are consistent with the automatic ones to avoid confusing analytics.
```

**Step 2: Commit**

```
docs(android): rewrite fragment tagging page with 3.10.0 readable names
```

---

## Phase 4: iOS Documentation Improvements

### Task 8: Expand iOS Configuration Options & Fix Quality Issues

**Files:**
- Modify: `docs/Mobile SDK Installation/ios/index.md`

**Step 1: Expand the configuration options section (after line 171)**

Replace the current 3-option section with comprehensive coverage. After the closing `</p>` of the `enableCrashHandling` option, add:

```markdown
<p style={{ fontSize: "18px" }}>
  <code className="language-swift">enableMultiSessionRecord</code>

  <br />

  <em style={{ fontSize: "13px" }}>Enable or disable recording of multiple sessions. Default is true.</em>
</p>

<p style={{ fontSize: "18px" }}>
  <code className="language-swift">enableIntegrationLogging</code>

  <br />

  <em style={{ fontSize: "13px" }}>Enable verbose integration logs in the console for debugging. Default is false. Recommended to enable only in DEBUG builds.</em>
</p>

<p style={{ fontSize: "18px" }}>
  <code className="language-swift">enableNetworkLogging</code>

  <br />

  <em style={{ fontSize: "13px" }}>Capture per-request network data (URL, method, status code, timing) for timeline correlation. Available since v3.7.9. Default is false.</em>
</p>

<p style={{ fontSize: "18px" }}>
  <code className="language-swift">occlusion</code>

  <br />

  <em style={{ fontSize: "13px" }}>Set a <code>UXCamOcclusion</code> object to pre-configure sensitive data masking at SDK start. See <a href="https://developer.uxcam.com/docs/sensitive-data-occlusion-ios">Sensitive Data Occlusion</a>.</em>
</p>

<p style={{ fontSize: "18px" }}>
  <code className="language-swift">environment</code>

  <br />

  <em style={{ fontSize: "13px" }}>Set the app environment: <code>.alpha</code>, <code>.beta</code>, or <code>.release</code>. Useful for filtering sessions by build type on the dashboard.</em>
</p>
```

**Step 2: Commit**

```
docs(ios): expand configuration options, add network logging and environment
```

---

### Task 9: Fill iOS Advanced Configuration Index Page

**Files:**
- Modify: `docs/Mobile SDK Installation/ios/advanced-configuration-and-apis-1/index.md`

**Step 1: Replace the empty file with an overview page:**

```markdown
---
title: Advanced Configuration and APIs
deprecated: false
hidden: false
metadata:
  robots: index
---

# Advanced Configuration & APIs

This section covers advanced UXCam iOS SDK features beyond the basic integration.

<Cards columns={2}>
  <Card title="Control Recording" href="control-recording-copy-1-ios" icon="fa-video">
    Start, stop, and manage session recording programmatically.
  </Card>

  <Card title="Crashes & ANRs" href="crashes-anrs-copy-ios" icon="fa-bug">
    How UXCam captures crash reports and ANR (App Not Responding) events.
  </Card>

  <Card title="Opt In / Opt Out" href="opt-in-opt-out-copy-ios" icon="fa-user-shield">
    Give users control over session recording and comply with privacy regulations.
  </Card>

  <Card title="Pause & Resume Recording" href="pause-and-resume-video-recording-copy-ios" icon="fa-pause-circle">
    Temporarily pause recording for sensitive flows without stopping the session.
  </Card>

  <Card title="Crash Symbolication" href="crash-symbolication-ios" icon="fa-code">
    Set up dSYM upload for readable crash stack traces on the dashboard.
  </Card>

  <Card title="Integration Logging" href="integration-logging-guide-for-developers-copy-1-ios" icon="fa-terminal">
    Enable verbose SDK logs for debugging your integration.
  </Card>

  <Card title="Report Handled Exceptions" href="report-handled-exceptions-copy-ios" icon="fa-exclamation-triangle">
    Send caught exceptions to UXCam for visibility alongside session replays.
  </Card>
</Cards>

---

## Configuration Reference

All options are set on the `UXCamConfiguration` object before calling `UXCam.start(with:)`:

```swift
let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")

// Screen tagging
config.enableAutomaticScreenNameTagging = true      // default: true

// Recording behavior
config.enableMultiSessionRecord = true              // default: true
config.enableCrashHandling = true                   // default: true
config.enableAdvancedGestureRecognition = true       // default: true

// Debugging
config.enableIntegrationLogging = false             // default: false

// Network
config.enableNetworkLogging = false                 // default: false

// Environment
config.environment = .release                       // .alpha | .beta | .release

// Privacy (set at init time)
config.occlusion = UXCamOcclusion(/* ... */)        // optional

UXCam.start(with: config)
```

> 📘 **Deprecation Note (v3.8.0+)**
>
> The `optIntoSchematicRecordings()` / `optOutOfSchematicRecordings()` methods are deprecated. Use `optIntoVideoRecordings()` / `optOutOfVideoRecordings()` instead. Schematic vs video recording is now controlled exclusively from the dashboard.
```

**Step 2: Commit**

```
docs(ios): fill advanced configuration index with overview and config reference
```

---

## Phase 5: Android Documentation Improvements

### Task 10: Fill Android Advanced Configuration Index Page

**Files:**
- Modify: `docs/Mobile SDK Installation/android/advanced-configuration-and-apis/index.md`

**Step 1: Replace the empty file with an overview page (same pattern as iOS Task 9, adapted for Android):**

Include:
- Card navigation to all sub-pages
- Full `UXConfig.Builder` reference with all options including:
  - `enableReadableFragmentNames(boolean)` (new in 3.10.0, default true)
  - `enableExperimentalForceQuitVideoRecovery()` (experimental)
  - `isVerboseLogEnabled(boolean)`
  - `isVerboseDebugModeEnabled(boolean)`
  - `honorFlagSecure(boolean)`
  - `environment(Environment)`
- Deprecation notice for `startWithKey()` methods

**Step 2: Commit**

```
docs(android): fill advanced configuration index with overview and config reference
```

---

## Phase 6: React Native Documentation Improvements

### Task 11: Improve React Native Events and User Properties Pages

**Files:**
- Modify: `docs/Mobile SDK Installation/react-native/events.md`
- Modify: `docs/Mobile SDK Installation/react-native/user-properties.md`

**Step 1: Expand events.md**

Add:
- Event naming best practices (max length, allowed characters)
- Event property types and limits
- Complete TypeScript example with proper typing
- Difference between events and session properties

**Step 2: Expand user-properties.md**

Add:
- When to use user properties vs session properties
- Property type constraints
- Example identifying a user after login
- How properties appear on the dashboard

**Step 3: Commit**

```
docs(react-native): improve events and user properties documentation
```

---

### Task 12: Improve React Native Expo Installation Guide

**Files:**
- Modify: `docs/Mobile SDK Installation/react-native/expo-installation-react-native.md`

**Step 1: Expand the page**

Add:
- Why Expo Go doesn't work (native modules requirement)
- Full EAS Build setup with `eas.json` example
- Development build instructions
- Common Expo-specific errors and solutions
- Link to main React Native setup for configuration after install

**Step 2: Commit**

```
docs(react-native): expand Expo installation guide
```

---

## Phase 7: Flutter Documentation Improvements

### Task 13: Fix Flutter Screen Tagging Contradictions

**Files:**
- Modify: `docs/Mobile SDK Installation/flutter/screen-tagging-flutter/index.md`
- Modify: `docs/Mobile SDK Installation/flutter/screen-tagging-flutter/navigator-route-tracking-flutter.md`

**Step 1: Clarify the relationship between automatic tagging and navigator observer**

The docs currently say both "use automatic tagging" and "disable automatic tagging" in different places. Clarify:
- `enableAutomaticScreenNameTagging: true` → SDK tags based on native view hierarchy (works for UIKit/Activity)
- `FlutterUxcamNavigatorObserver` → tracks Flutter `Navigator` route changes (recommended for Flutter apps)
- Best practice: use the Navigator Observer with `enableAutomaticScreenNameTagging: false` for Flutter apps

**Step 2: Commit**

```
docs(flutter): clarify screen tagging approach and resolve contradictions
```

---

### Task 14: Improve Flutter Troubleshooting Page

**Files:**
- Modify: `docs/Mobile SDK Installation/flutter/troubleshooting-flutter.md`

**Step 1: Fix the incorrect Flutter version reference**

The page says "Flutter SDK version 3.10+ is installed" — this refers to the Flutter framework version, but could be confused with UXCam SDK version. Clarify which version is meant.

**Step 2: Add common Flutter-specific issues:**
- Impeller rendering compatibility
- `PlatformView` occlusion behavior
- Hot reload vs hot restart behavior with UXCam
- iOS archive/release build issues
- ProGuard rules for Android release builds

**Step 3: Commit**

```
docs(flutter): improve troubleshooting with common Flutter-specific issues
```

---

## Phase 8: Cross-Platform Changelog Stubs

### Task 15: Replace React Native and Flutter Changelog Stubs

**Files:**
- Modify: `docs/Mobile SDK Installation/react-native/react-native-sdk-change-log-react-native.md`
- Modify: `docs/Mobile SDK Installation/flutter/flutter-sdk-changelog-flutter.md`

**Step 1: Replace the empty stub cards with actual content or a proper redirect**

Option A (preferred): Add the 5 most recent versions inline + link to full changelog
Option B: Make the redirect card more useful with latest version info

**Step 2: Commit**

```
docs(changelog): replace empty changelog stubs with recent version summaries
```

---

## Phase 9: Cordova / NativeScript / Xamarin Quality Pass

### Task 16: Fix Cordova OkHttp Troubleshooting and Links

**Files:**
- Modify: `docs/Mobile SDK Installation/cordova/index.md`

**Step 1: Update the old help center link**

Replace the outdated `help.uxcam.com` link with the developer docs changelog link.

**Step 2: Verify OkHttp version recommendation is still current**

The doc recommends `okhttp:4.9.3` — update to a more recent version if appropriate (e.g., `4.12.0`).

**Step 3: Commit**

```
docs(cordova): update links and dependency versions
```

---

### Task 17: Fix NativeScript Outdated Links

**Files:**
- Modify: `docs/Mobile SDK Installation/nativescript/index.md`

**Step 1: Remove the outdated help center link and emoji usage**

Clean up the page formatting for consistency with other platform pages.

**Step 2: Commit**

```
docs(nativescript): clean up formatting and outdated links
```

---

## Execution Order & Dependencies

```
Phase 1 (Tasks 1-2)  → Changelogs — no dependencies, do first
Phase 2 (Tasks 3-6)  → Deprecated APIs — no dependencies, can parallel
Phase 3 (Task 7)     → Android 3.10.0 content — depends on Task 1 (changelog)
Phase 4 (Tasks 8-9)  → iOS improvements — depends on Task 3 (deprecated fix)
Phase 5 (Task 10)    → Android improvements — no dependency
Phase 6 (Tasks 11-12)→ React Native — depends on Task 4
Phase 7 (Tasks 13-14)→ Flutter — depends on Task 5
Phase 8 (Task 15)    → Changelog stubs — no dependency
Phase 9 (Tasks 16-17)→ Cross-platform — depends on Task 6
```

**Parallelizable groups:**
- Tasks 1 + 2 (both changelogs)
- Tasks 3 + 4 + 5 + 6 (all deprecated API fixes, different platforms)
- Tasks 7 + 8 + 9 + 10 (new content, different platforms)
- Tasks 11 + 12 + 13 + 14 (quality improvements, different platforms)
- Tasks 15 + 16 + 17 (stubs and cross-platform fixes)

**Total estimated files touched:** ~25-30
**Total commits:** 17