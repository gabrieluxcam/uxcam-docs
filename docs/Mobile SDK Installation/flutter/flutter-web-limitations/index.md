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

The UXCam Flutter SDK supports both mobile (iOS/Android) and Flutter Web, but some advanced features are not yet available on the web platform.

## Not Supported on Flutter Web

The following features and APIs are not currently supported when running on Flutter Web:

- **Recording Control** — programmatic start, stop, and pause of session recording
- **Crash & ANR Handling** — automatic crash capture and ANR reporting
- **User Consent Management** — opt-in / opt-out APIs
- **Debug & Logging** — verbose integration logging
- **Sensitive Data Occlusion** — overlay, blur, and dashboard-driven occlusion rules

If your app targets Flutter Web in addition to mobile, guard these calls behind a platform check so they only run on supported platforms.

## Need Help?

If a feature you rely on is listed here, contact [team@uxcam.com](mailto:team@uxcam.com) — we can advise on workarounds and share roadmap updates.
