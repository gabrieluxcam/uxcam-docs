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

The UXCam Flutter SDK ships from a single plugin and supports both mobile (iOS / Android) and Flutter Web. The core analytics experience — session recording, screen tagging, events, and user properties — works on every platform. A few advanced APIs are mobile-only today; this page is the reference for which is which.

## Feature Support Matrix

**Legend:** ✅ Supported · 🟡 Core supported · 🚧 On the roadmap · 🔬 Under exploration · ➖ Not applicable on web

| Feature                      | Mobile (iOS / Android) |   Flutter Web   |
| ---------------------------- | :--------------------: | :-------------: |
| Session recording            |           ✅            |        ✅        |
| Screen tagging               |           ✅            |        ✅        |
| Events & user properties     |           ✅            |        ✅        |
| Sensitive data occlusion     |           ✅            | 🟡  |
| Heatmaps                     |           ✅            |       🔬        |
| Tara AI                      |           ✅            |       🚧        |
| Recording control APIs       |           ✅            |        ➖        |
| Crash & ANR handling         |           ✅            |        ➖        |
| User consent management      |           ✅            |        ➖        |
| Debug & verbose logging      |           ✅            |        ➖        |
| `allowShortBreak` API        |           ✅            |        ➖        |

## How Flutter Web Sessions Differ

Flutter Web sessions are **video-based** rather than DOM-based, so the replay experience differs slightly from the standalone UXCam Web SDK.

### Tap gestures instead of mouse movement

Mouse cursor movement is not rendered in the replay. Instead, you'll see tap gesture indicators on the video — the same way they appear in mobile session replays.

## Sensitive Data Occlusion on Web

The core occlusion API is supported on Flutter Web and covers the most common use cases — masking widgets that contain sensitive content. Advanced controls (overlay/blur modes, fine-grained per-view rules, and dashboard-driven occlusion rules) are not yet available on web. For cross-platform behavior, stick to the standard occlusion APIs.

## On the Roadmap

These features are mobile-only today and are planned for Flutter Web:

- **Heatmaps** — tap and gesture heatmaps for web sessions
- **Tara AI** — AI-driven session insights

## Mobile-Only APIs

These APIs are specific to the mobile runtime and aren't part of the Flutter Web scope. Calling them on web is safe — they're no-ops and won't crash your app — but they won't produce dashboard results.

- **Recording Control** — programmatic `start`, `stop`, `pause`, and `allowShortBreak` of session recording
- **Crash & ANR Handling** — native crash capture and ANR reporting
- **User Consent Management** — opt-in / opt-out APIs
- **Debug & Verbose Logging** — native integration logging

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
