---
title: Flutter Web Platform Support
excerpt: What's supported on Flutter Web and how it compares to mobile
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    Feature support reference for the UXCam Flutter SDK across Flutter Web and
    mobile (iOS / Android).
  robots: index
next:
  description: ''
---

# Flutter Web Platform Support

The UXCam Flutter SDK ships from a single plugin and supports both mobile (iOS / Android) and Flutter Web. The core analytics experience — session recording, screen tagging, events, and user properties — works on every platform. A few advanced APIs are mobile-only today; this page is the reference for which is which.

## Feature Support Matrix

| Feature                      | Mobile (iOS / Android) | Flutter Web |
| ---------------------------- | :--------------------: | :---------: |
| Session recording            |           ✅            |      ✅      |
| Screen tagging               |           ✅            |      ✅      |
| Events & user properties     |           ✅            |      ✅      |
| Sensitive data occlusion     |           ✅            |   ⚠️ Partial   |
| Recording control APIs       |           ✅            |      ❌      |
| Crash & ANR handling         |           ✅            |      ❌      |
| User consent management      |           ✅            |      ❌      |
| Debug & verbose logging      |           ✅            |      ❌      |
| `allowShortBreak` API        |           ✅            |      ❌      |
| Heatmaps                     |           ✅            |      ❌      |
| Tara AI                      |           ✅            |      ❌      |

## How Flutter Web Sessions Differ

Flutter Web sessions are **video-based** rather than DOM-based, so the replay experience differs slightly from the standalone UXCam Web SDK.

### Tap gestures instead of mouse movement

Mouse cursor movement is not rendered in the replay. Instead, you'll see tap gesture indicators on the video — the same way they appear in mobile session replays.

### No heatmaps

Heatmap generation is not supported for Flutter Web sessions.

## Mobile-Only APIs

The following APIs are no-ops on Flutter Web. Calling them won't crash your app, but they will not produce dashboard results.

- **Recording Control** — programmatic `start`, `stop`, `pause`, and `allowShortBreak` of session recording
- **Crash & ANR Handling** — automatic crash capture and ANR reporting
- **User Consent Management** — opt-in / opt-out APIs
- **Debug & Logging** — verbose integration logging

### Partial: Sensitive Data Occlusion

Basic occlusion is supported on Flutter Web, but advanced controls (overlay/blur modes, fine-grained per-view rules, and dashboard-driven occlusion rules) are not yet available. Stick to the standard occlusion APIs for cross-platform behavior.

## Recommended Pattern: Guard Mobile-Only Calls

Wrap mobile-only calls in a `kIsWeb` check so the same codebase works across platforms:

```dart
import 'package:flutter/foundation.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';

if (!kIsWeb) {
  FlutterUxcam.optIntoVideoRecordings();
  // other mobile-only APIs...
}
```

## Need Help?

If a feature you rely on isn't supported yet, contact [team@uxcam.com](mailto:team@uxcam.com) — we can advise on workarounds and share roadmap updates.
