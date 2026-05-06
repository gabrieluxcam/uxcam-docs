---
title: SDK Reference
excerpt: API method signatures and usage across all platforms
deprecated: false
hidden: false
metadata:
  title: 'UXCam SDK Reference'
  description: 'Complete API reference for UXCam SDKs across Android, iOS, React Native, Flutter, and Web'
  robots: index
next:
  description: ''
---

# SDK Reference

This reference documents UXCam SDK methods available across all platforms. For platform-specific implementation guides, see the [Mobile SDK](/docs/mobile-sdk-installation) or [Web SDK](/docs/web-sdk-installation) documentation.

---

## Core APIs

### Configuration & Initialization

| Method | Description |
|--------|-------------|
| `startWithConfiguration(config)` | Initialize the SDK with configuration options |
| `optIntoVideoRecordings()` | Enable screen recording (required for iOS/Android) |

<Card title="Configuration Options" href="/docs/android" icon="fa-solid fa-gear">
  See platform guides for all configuration parameters
</Card>

---

### Screen Tagging

Tag screens for analytics and heatmaps.

| Method | Description |
|--------|-------------|
| `tagScreenName(screenName)` | Manually tag the current screen |

```swift
// iOS
UXCam.tagScreenName("Home Screen")
```
```kotlin
// Android
UXCam.tagScreenName("Home Screen")
```
```javascript
// React Native
RNUxcam.tagScreenName("Home Screen")
```
```dart
// Flutter
FlutterUxcam.tagScreenName("Home Screen")
```

<Cards columns={2}>
  <Card title="Screen Tagging Guide" href="/docs/tag-of-screens" icon="fa-solid fa-mobile">
    Complete screen tagging documentation with examples
  </Card>
  <Card title="Platform-Specific Guides" href="/docs/screen-tagging" icon="fa-solid fa-code">
    Android, iOS, Flutter navigation integration
  </Card>
</Cards>

---

### Events

Track custom user actions.

| Method | Description |
|--------|-------------|
| `logEvent(eventName)` | Log an event |
| `logEvent(eventName, properties)` | Log an event with properties |

```swift
// iOS
UXCam.logEvent("Purchase", withProperties: ["item": "Pro Plan", "price": 9.99])
```
```kotlin
// Android
UXCam.logEvent("Purchase", mapOf("item" to "Pro Plan", "price" to 9.99))
```
```javascript
// React Native
RNUxcam.logEventWithProperties("Purchase", { item: "Pro Plan", price: 9.99 })
```
```dart
// Flutter
FlutterUxcam.logEventWithProperties("Purchase", {"item": "Pro Plan", "price": 9.99})
```

<Card title="Events Guide" href="/docs/customization-apis" icon="fa-solid fa-bolt">
  Complete event tracking documentation
</Card>

---

### User Identification

Associate sessions with user identifiers.

| Method | Description |
|--------|-------------|
| `setUserIdentity(userId)` | Set the user identifier |
| `setUserProperty(key, value)` | Set a user property |

```swift
// iOS
UXCam.setUserIdentity("user_12345")
UXCam.setUserProperty("subscription", value: "premium")
```
```kotlin
// Android
UXCam.setUserIdentity("user_12345")
UXCam.setUserProperty("subscription", "premium")
```
```javascript
// React Native
RNUxcam.setUserIdentity("user_12345")
RNUxcam.setUserProperty("subscription", "premium")
```
```dart
// Flutter
FlutterUxcam.setUserIdentity("user_12345")
FlutterUxcam.setUserProperty("subscription", "premium")
```

<Card title="User Properties Guide" href="/docs/customization-apis" icon="fa-solid fa-user">
  Complete user identification documentation
</Card>

---

## Privacy APIs

### Occlusion (Sensitive Data)

Hide sensitive content in recordings.

| Method | Description |
|--------|-------------|
| `occludeSensitiveView(view)` | Hide a specific view |
| `occludeSensitiveScreen(true)` | Hide the entire screen |
| `occludeAllTextFields(true)` | Hide all text input fields |

```swift
// iOS
UXCam.occlude(sensitiveView)
UXCam.occludeAllTextFields(true)
```
```kotlin
// Android
UXCam.occludeSensitiveView(sensitiveView)
UXCam.occludeAllTextFields(true)
```

<Card title="Occlusion Guide" href="/docs/screen-blurring" icon="fa-solid fa-eye-slash">
  Complete occlusion documentation with platform examples
</Card>

---

### Consent & Opt-Out

Manage user consent for recording.

| Method | Description |
|--------|-------------|
| `optOutOverall()` | Stop all recording for this user |
| `optIn()` | Resume recording (if previously opted out) |
| `optOutStatus()` | Check if user has opted out |

```swift
// iOS
UXCam.optOutOverall()
let isOptedOut = UXCam.optOutStatus()
UXCam.optIn()
```
```kotlin
// Android
UXCam.optOutOverall()
val isOptedOut = UXCam.optOutStatus()
UXCam.optIn()
```

<Card title="Opt-In/Out Guide" href="/docs/opt-in-opt-out" icon="fa-solid fa-toggle-on">
  Complete consent management documentation
</Card>

---

## Session Control APIs

### Recording Control

Control when recording happens.

| Method | Description |
|--------|-------------|
| `stopSessionAndUploadData()` | End session and upload |
| `pauseScreenRecording()` | Pause screen capture |
| `resumeScreenRecording()` | Resume screen capture |
| `isRecording()` | Check if currently recording |

```swift
// iOS
UXCam.stopSessionAndUploadData()
let recording = UXCam.isRecording()
```
```kotlin
// Android
UXCam.stopSessionAndUploadData()
val recording = UXCam.isRecording()
```

<Card title="Recording Control Guide" href="/docs/control-recording-1" icon="fa-solid fa-circle-stop">
  Complete recording control documentation
</Card>

---

### Crash Handling

Configure crash and exception reporting.

| Method | Description |
|--------|-------------|
| `reportExceptionEvent(exception)` | Report a handled exception |
| Configuration: `enableCrashHandling` | Enable/disable crash capture |

<Card title="Crash Handling Guide" href="/docs/control-crash-handling" icon="fa-solid fa-bug">
  Crash and exception reporting documentation
</Card>

---

## Debugging APIs

### Integration Logging

| Method | Description |
|--------|-------------|
| Configuration: `enableIntegrationLogging` | Enable verbose debug logs |
| `urlForCurrentSession()` | Get dashboard URL for current session |

```swift
// iOS
config.enableIntegrationLogging = true
if let url = UXCam.urlForCurrentSession() {
    print("Session URL: \(url)")
}
```
```kotlin
// Android
val config = UXConfig.Builder(key)
    .enableIntegrationLogging(true)
    .build()
val url = UXCam.urlForCurrentSession()
```

<Card title="Debugging Guide" href="/docs/integration-logging-guide-for-developers" icon="fa-solid fa-terminal">
  Integration logging documentation
</Card>

---

## Platform-Specific APIs

Some features have platform-specific implementations:

| Feature | Platforms | Guide |
|---------|-----------|-------|
| Jetpack Compose Tagging | Android | [Guide](/docs/jetpack-compose-screen-tagging) |
| SwiftUI Tagging | iOS | [Guide](/docs/swiftui-automatic-screen-tagging) |
| Flutter Route Tracking | Flutter | [Guide](/docs/flutter-tagging-approach) |
| Jetpack Compose Occlusion | Android | [Guide](/docs/jetpack-compose-occlusion) |
| WebView Handling | All | [Guide](/docs/web-view-tagging) |

---

## See Also

- [Getting Started](/docs/getting-started) - Platform installation guides
- [Concepts](/docs/concepts) - How UXCam works
- [Data Access API](/docs/data-access-api) - REST API for exporting data
