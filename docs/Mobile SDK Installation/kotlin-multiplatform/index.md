---
title: Kotlin Multiplatform
excerpt: Install and initialize UXCam in a Kotlin Multiplatform (KMP) project.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
# UXCam Kotlin Multiplatform Integration Guide

Welcome! This guide will help you set up UXCam in your Kotlin Multiplatform project, enabling you to gather insights that are crucial for improving user experience. The UXCam KMP SDK gives you one shared Kotlin API that binds to the native UXCam SDK on Android and iOS — write your analytics code once in `commonMain` and get full native session recording on both platforms.

## What Does a Successful Integration Look Like?

With a complete UXCam integration you'll be able to track user journeys, analyze user behavior, and watch session replays for both your Android and iOS apps — driven from your shared Kotlin code. Following this guide, you'll have your KMP app connected to UXCam in no time.

### Minimum Requirements

| Requirement | Version |
| :-- | :-- |
| Kotlin | 2.4.0+ |
| JDK | 17 |
| Android | compile SDK 35+, device API 24+ |
| iOS | 15+ |
| Xcode | 26+ |

## Quick Start Guide

### Step 1: Apply the UXCam Gradle Plugin

Apply the UXCam plugin to your **shared module**:

```kotlin
plugins {
    id("com.uxcam.kmp") version "1.0.0"
}
```

The plugin adds `uxcam-kmp` to `commonMain` and adds `uxcam-compose` automatically when Compose Multiplatform is detected. The Android app receives the native UXCam SDK transitively — no extra Android setup.

> 📘 Multi-module projects
>
> Declare the Kotlin and Android plugins once in the root build with `apply false`, then apply the UXCam plugin in the shared module as shown above.

### Step 2: Link the iOS SDK

The native UXCam iOS SDK must be linked by the **final iOS application**:

* **Swift Package Manager:** add `https://github.com/uxcam/uxcam-ios` in Xcode, and use a **static** Kotlin framework for your shared module.
* **CocoaPods:** when the shared module uses `kotlin("native.cocoapods")`, the UXCam plugin adds the native pod automatically — nothing to do.

### Step 3: Start UXCam

Start UXCam from your shared code with your App Key, which you can find on your [UXCam dashboard](https://app.uxcam.com):

```kotlin
import com.uxcam.kmp.KMPUXCamBlur
import com.uxcam.kmp.UXCamKMP
import com.uxcam.kmp.uxcamConfiguration

UXCamKMP.startWithConfiguration(
    uxcamConfiguration("YOUR_UXCAM_APP_KEY") {
        enableCrashHandling = true
        occlusions = listOf(
            KMPUXCamBlur(screens = listOf("PaymentScreen")),
        )
    },
)
```

Configuration options: `enableMultiSessionRecord` (default `true`), `enableCrashHandling` (default `true`), `enableIntegrationLogging` (default `false` — enable while integrating to see SDK logs), and `occlusions` (startup occlusion rules, see below).

## Screen Tagging

Screen names are tagged **manually** — there is no automatic screen-name tagging in the KMP SDK:

```kotlin
UXCamKMP.tagScreenName("Home")
```

Tag from wherever your shared navigation logic knows the current screen (e.g. your navigation state holder). Screens can also be excluded from recording with the ignore-list APIs: `addScreenNameToIgnore`, `addScreenNamesToIgnore`, `removeScreenNameToIgnore`, `removeAllScreenNamesToIgnore`, and `screenNamesBeingIgnored`.

## Events, Users, and Sessions

The shared API mirrors the developer-facing surface of the native SDKs:

```kotlin
UXCamKMP.logEvent("checkout_started", mapOf("cart_size" to 3))
UXCamKMP.setUserIdentity("user-42")
UXCamKMP.setUserProperty("plan", "pro")
UXCamKMP.setSessionProperty("experiment", "checkout-v2")
```

Also available from common code: session control (`startNewSession`, `stopSessionAndUploadData`, `cancelCurrentSession`), bug and exception reporting (`reportBugEvent`, `reportExceptionEvent`), recording controls (`pauseScreenRecording`, `resumeScreenRecording`, `isRecording`, `allowShortBreakForAnotherApp`), consent management (`optInOverall`, `optOutOverall`, `optIntoVideoRecording`, `optOutOfVideoRecording`), verification callbacks (`addVerificationListener`), session/user dashboard URLs (`urlForCurrentSession`, `urlForCurrentUser`), upload status (`pendingSessionCount`, `pendingUploads`, `deletePendingUploads`), and `getSdkVersionInfo`.

## Sensitive Data Occlusion

Occlusion rules describe **what** to hide (overlay or blur) and **where** (screen names, or everywhere except listed screens):

```kotlin
// At startup (required for excludeMentionedScreens on iOS):
uxcamConfiguration("YOUR_UXCAM_APP_KEY") {
    occlusions = listOf(
        KMPUXCamOverlay(screens = listOf("CardEntry")),
        KMPUXCamBlur(blurRadius = 15, screens = listOf("PaymentScreen")),
    )
}

// At runtime (additive until removeOcclusion() is called):
UXCamKMP.applyBlurOcclusion(KMPUXCamBlur(screens = listOf("Settings")))
UXCamKMP.occludeSensitiveScreen(true)
```

### Compose Multiplatform

For Compose UI, occlude individual composables with the `uxcam-compose` modifier (added automatically by the Gradle plugin when Compose is detected):

```kotlin
Text(
    text = "Sensitive content",
    modifier = Modifier.uxcamOcclude("payment-card"),
)
```

## Limitations & Platform Notes

* **Session recording happens on Android and iOS only.** JVM (desktop), JavaScript, WebAssembly, Linux, and Windows are **no-op targets**: the full API is callable from shared code without platform guards, but nothing is recorded on those platforms.
* **No automatic screen tagging.** All screens are tagged manually with `tagScreenName` (see above).
* **The iOS app must link the native iOS SDK itself** (Step 2). The Gradle plugin only handles this automatically in the CocoaPods setup; with SPM it is a manual Xcode step, and the shared Kotlin framework must be static.
* **Per-view occlusion is platform-specific.** There is no common `View` type across platforms, so occluding a single native view is done from platform source sets (`android.view.View` on Android, `UIView` on iOS), not from `commonMain`. From common code you have screen-level rules, `occludeSensitiveScreen`, and `Modifier.uxcamOcclude` for Compose.
* **iOS cannot remove configuration-based occlusions at runtime.** `removeOcclusion()` clears rules applied via `applyOverlayOcclusion`/`applyBlurOcclusion` on both platforms — but rules passed in `UXCamConfiguration.occlusions` (including any requested before start) persist for the app's lifetime on iOS. On Android all rules are removed.
* **`excludeMentionedScreens` requires startup configuration on iOS.** To occlude everywhere *except* certain screens on iOS, the rule must be in `UXCamConfiguration.occlusions` — not applied at runtime.
* **Blur algorithm selection is honored on iOS only.** Android uses its single blur implementation and honors the blur radius only; the `BlurType` choice affects iOS.
* **Platform-only native APIs are not in the common API.** Features that exist on one native SDK only are exposed as platform source-set extensions rather than common methods that silently do nothing. Where the native SDKs name the same feature differently, the Android name is used.

## Installing Without the Gradle Plugin

You can add the shared library directly, but must configure iOS linking yourself:

```kotlin
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("com.uxcam:uxcam-kmp:1.0.0")
        }
    }
}
```

## Native iOS Application

To call the shared UXCam API from Swift in the iOS app itself, add the prebuilt package in Xcode and `import UXCamKMP`:

```text
https://github.com/uxcam/uxcam-kmp
```

## Troubleshooting

* Set `enableIntegrationLogging = true` in the configuration while integrating to see SDK logs.
* Verify the integration with `addVerificationListener` — the success callback confirms your App Key verified and the session started.
* If iOS builds fail to find UXCam symbols, confirm the final application links the native iOS SDK (Step 2) and that the shared framework is static when using SPM.
