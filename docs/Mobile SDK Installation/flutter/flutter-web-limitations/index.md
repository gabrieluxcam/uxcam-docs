---
title: Flutter Web Limitations
excerpt: Features and APIs that are not currently supported on Flutter Web
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    Overview of UXCam Flutter SDK features that are not available when running
    on Flutter Web.
  robots: index
next:
  description: ''
---

# Flutter Web Limitations

The UXCam Flutter SDK supports both mobile (iOS/Android) and Flutter Web from the same plugin, but a subset of advanced features is not yet available on the web platform.
## Behavioral Differences from the normal Web SDK

Flutter Web sessions are **video-based** rather than DOM-based, so a few things behave differently from the standalone UXCam Web SDK.

### Tap gestures instead of mouse movement

Mouse cursor movement is not rendered in the replay. Instead, you'll see tap gesture indicators on the video — the same way they appear in mobile session replays.

### No heatmaps

Heatmap generation is not supported for Flutter Web sessions.



> ❗️ Cross-platform safety
>
> If you target Flutter Web alongside mobile, guard the unsupported APIs behind a `kIsWeb` platform check so they only run on iOS/Android.

## Feature Support Matrix

| Feature                      | Mobile (iOS / Android) | Flutter Web |
| ---------------------------- | :--------------------: | :---------: |
| Session recording            |           ✅            |      ✅      |
| Screen tagging               |           ✅            |      ✅      |
| Events & user properties     |           ✅            |      ✅      |
| Recording control APIs       |           ✅            |      ❌      |
| Crash & ANR handling         |           ✅            |      ❌      |
| User consent management      |           ✅            |      ❌      |
| Debug & verbose logging      |           ✅            |      ❌      |
| Sensitive data occlusion     |           ✅            |   ⚠️ Partial   |
| `allowShortBreak` API        |           ✅            |      ❌      |
| Heatmaps                     |           ✅            |      ❌      |

## Unsupported APIs on Flutter Web

The following APIs are no-ops or unavailable when running on Flutter Web. Calling them won't crash your app, but they will not produce dashboard results.

- **Recording Control** — programmatic `start`, `stop`, `pause` and *allowShortBreak* of session recording
- **Crash & ANR Handling** — automatic crash capture and ANR reporting
- **User Consent Management** — opt-in / opt-out APIs
- **Debug & Logging** — verbose integration logging

### Partially Supported

- **Sensitive Data Occlusion** — basic occlusion is supported on Flutter Web, but advanced controls (overlay/blur modes, fine-grained per-view rules, and dashboard-driven occlusion rules) are not yet available. Stick to the standard occlusion APIs for cross-platform behavior.

### Recommended Pattern

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

If a feature you rely on is listed here, contact [team@uxcam.com](mailto:team@uxcam.com) — we can advise on workarounds and share roadmap updates.
