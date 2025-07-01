---
title: Sensitive Data Occlusion In iOS Apps
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document outlines how to handle sensitive data in iOS applications using
    UXCam's features to ensure privacy compliance, emphasizing the occlusion of
    Personally Identifiable Information (PII) like passwords and credit card
    numbers through various methods such as text, screen views, and entire
    screens, either via SDK code or directly from the UXCam dashboard. It also
    provides guidance on configuring occlusion settings, including overlay and
    blur options, to protect user data while maintaining the ability to record
    gestures if needed.
  robots: index
next:
  description: >-
    Excellent! You can record some test sessions and once you're finalised with
    occlusion, we can move onto custom events and user properties!
  pages:
    - type: basic
      slug: custom-users-and-properties-copy
      title: Custom Users and Properties (COPY)
---
Good privacy practices are **non‑negotiable**—especially under GDPR, CCPA and Apple’s App Store policies. UXCam lets you decide **what never gets recorded** at three levels: text fields, views, or entire screens.

> 📘 **UIKit vs SwiftUI** The APIs below come in *both* flavours: `UXCam` for UIKit and `UXCamSwiftUI` helpers built on `UXCamCore`.

***

# 1 · What’s hidden by default?

| Platform    | Occluded out of the box                                                                                |
| ----------- | ------------------------------------------------------------------------------------------------------ |
| **UIKit**   | `UITextField` with `UITextContentType` of `password`, `creditCardNumber`, `newPassword`, `oneTimeCode` |
| **SwiftUI** | Any `TextField` or `SecureField` wrapped in `.uxcamOcclude()`                                          |

No code required—these never reach UXCam servers.

***

# 2 · Dashboard‑first rules (zero‑code)

1. **Settings → Video Recording Privacy** in your UXCam dashboard.
2. Choose **Record • Occlude • Blur** globally *or* per screen.
3. (Optional) **Blur radius** selector and **Record gestures** toggle.
4. Save → record a test session to verify.

> 🛠️ **Priority ladder**\
> Screen‑specific Dashboard rule → Global Dashboard rule → SDK overlay/blur → SDK text‑field occlusion.

***

# 3 · One‑liner occlusion APIs

### 3.1 Overlay an entire screen

```swift
let yellow = UXCamOverlaySetting(color: .systemYellow)
UXCam.applyOcclusion(yellow) // UIKit
// SwiftUI: UXCamCore.applyOcclusion(yellow)
```

### 3.2 Blur with custom radius

```swift
let blur = UXCamBlurSetting(radius: 6, hideGestures: false)
UXCam.applyOcclusion(blur)
```

### 3.3 Hide *all* text inputs

```swift
UXCam.applyOcclusion(UXCamOccludeAllTextFields())
```

### 3.4 Hide a single sensitive view

```swift
passwordField.uxcamOcclude()                 // SwiftUI
UXCam.occludeSensitiveView(cardNumberView)   // UIKit
```

### 3.5 Remove occlusion

```swift
UXCam.removeOcclusion()            // everything
UXCam.removeOcclusion(of: .blur)   // just blurs
```

***

# 4 · Apply at config time (recommended)

```swift
let blur = UXCamBlurSetting(radius: 5)
let occlusion = UXCamOcclusion()
occlusion.apply(blur, screens: ["CheckoutViewController"]) // or SwiftUI tag

let config = UXCamConfiguration(appKey: key)
config.occlusion = occlusion
UXCam.start(with: config)
```

This keeps all rules **in one place** and avoids race conditions.

***

# 5 · Sample SwiftUI snippet

```swift
init() {
  UXCamCore.optIntoSchematicRecordings()
  let cfg = Configuration(appKey: key)
  cfg.enableAutomaticScreenNameTagging = true
  UXCamSwiftUI.start(with: cfg)
}

var body: some View {
  SecureField("Password", text: $pwd)
     .uxcamOcclude(blockGestures: true) // hide & block gestures
}
```

***

# 6 · Verification checklist

1. Record a debug session through sensitive flows.
2. Play it back—passwords & cards must be *red‑boxed or blurred*.
3. Check **Screen list**: no 0 s duplicates.
4. Use the “Record gestures” toggle if taps should *not* be shown.

***

# 7 · Troubleshooting quick‑ref

| Issue                                    | Cause                               | Fix                                    |
| ---------------------------------------- | ----------------------------------- | -------------------------------------- |
| Overlay shows but gestures still visible | `hideGestures` defaulted to `false` | Set `hideGestures = true`              |
| Blur only on some devices                | `radius` too low on @3x screens     | Use ≥ 5 or switch to overlay           |
| Text inputs still visible                | Custom `UITextField` subclass       | Call `uxcamOcclude()` manually         |
| WebView leaks card numbers               | Bridge not injected                 | See *Sensitive Data in WebViews* guide |

***