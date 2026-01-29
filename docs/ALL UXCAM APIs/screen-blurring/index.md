---
title: Sensitive Data Occlusion and Screen Blurring
excerpt: 'API reference for hiding sensitive data in session recordings'
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
UXCam ensures you can fulfil GDPR obligations. If you collect **PII data** (email, phone, credit card), use our APIs to hide it before it reaches UXCam servers.

## Occlusion Methods Overview

| Method | Purpose | Platforms |
|--------|---------|-----------|
| **Overlay** | Cover entire screen with solid color | All |
| **Blur** | Blur entire screen (adjustable radius) | All |
| **Occlude All Text Fields** | Hide all text inputs | All except Flutter |
| **Occlude Sensitive View** | Hide specific UI elements | All |

## Default Occlusions (No Code Required)

| Platform | Auto-Occluded Elements |
|----------|----------------------|
| **iOS** | Text fields with `password`, `creditCardNumber`, `newPassword`, `oneTimeCode` content types |
| **Android** | Password fields with `textPassword` input type |
| **React Native / SwiftUI / Ionic** | Elements with `uxcam-occlude` class or `type="password"` |
| **Flutter** | None by default (requires wrapper) |
| **Jetpack Compose** | None (requires manual setup) |

## Dashboard Configuration (Recommended)

Configure occlusion rules without code changes via **App Settings > Video Recording Privacy**:

- **Global rules**: Record, occlude, or blur all screens
- **Screen-specific rules**: Different rules per screen
- **Text field occlusion**: Hide all inputs on selected screens

Requires: iOS 3.6.0+, Android 3.6.0+, Flutter 2.3.1+, React Native 5.4.6+

---

## SDK API Reference

### Apply Overlay

Covers the screen with a solid color. Sensitive content is never recorded.

```java Android
UXCamOverlay overlay = new UXCamOverlay.Builder()
    .withoutGesture(false)  // true = hide gestures (default)
    .screens(Arrays.asList("Screen1"))  // optional
    .build();
UXCam.applyOcclusion(overlay);
UXCam.removeOcclusion(overlay);
```
```swift iOS
let overlay = UXCamOverlaySetting(color: .yellow)
UXCam.applyOcclusion(overlay)
UXCam.removeOcclusion(of: .overlay)
```
```dart Flutter
FlutterUXOverlay overlay = FlutterUXOverlay(color: Colors.red, hideGestures: true);
FlutterUxcam.applyOcclusion(overlay);
FlutterUxcam.removeOcclusion(overlay);
```
```javascript React Native
const overlay = { type: UXCamOcclusionType.Overlay, color: 0xff00ee, hideGestures: true };
RNUxcam.applyOcclusion(overlay);
RNUxcam.removeOcclusion(overlay);
```
```swift SwiftUI
UXCamCore.applyOcclusion(OverlaySetting())
UXCamCore.removeOcclusion()
```
```csharp Xamarin
UXCam.applyOcclusion(new UXCamOverlaySetting(UIColor.Yellow));
```
```javascript Cordova
UXCam.applyOcclusion({ type: UXCamOcclusionType.Overlay, color: 0xff00ee });
```

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `color` | Color | Overlay color |
| `hideGestures` / `withoutGesture` | Boolean | Hide gesture recording (default: true) |
| `screens` | List | Apply to specific screens only |
| `excludeMentionedScreens` | Boolean | If true, apply to all screens *except* listed ones |

---

### Apply Blur

Blurs the screen while preserving layout visibility. Useful for maintaining context without exposing details.

```java Android
UXCamBlur blur = new UXCamBlur.Builder()
    .blurRadius(10)  // default 20
    .withoutGesture(false)
    .build();
UXCam.applyOcclusion(blur);
```
```swift iOS
let blur = UXCamBlurSetting(radius: 5)
UXCam.applyOcclusion(blur)
UXCam.removeOcclusion(of: .blur)
```
```dart Flutter
FlutterUXBlur blur = FlutterUXBlur(blurRadius: 10, blurType: BlurType.gaussian);
FlutterUxcam.applyOcclusion(blur);
```
```javascript React Native
const blur = { type: UXCamOcclusionType.Blur, blurRadius: 20, hideGestures: true };
RNUxcam.applyOcclusion(blur);
```
```swift SwiftUI
UXCamCore.applyOcclusion(BlurSetting())
```
```csharp Xamarin
UXCam.applyOcclusion(new UXCamBlurSetting(10));
```
```javascript Cordova
UXCam.applyOcclusion({ type: 3, blurRadius: 20 });
```

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `blurRadius` | Integer | Blur strength (higher = more blur) |
| `hideGestures` | Boolean | Hide gesture recording (default: true) |
| `screens` | List | Apply to specific screens only |

---

### Occlude All Text Fields

Hides all text input fields on screen.

```java Android
UXCam.applyOcclusion(new UXCamOccludeAllTextFields());
```
```swift iOS
UXCam.applyOcclusion(UXCamOccludeAllTextFields())
```
```text Flutter
Not supported - use OccludeWrapper instead
```
```javascript React Native
RNUxcam.applyOcclusion({ type: UXCamOcclusionType.OccludeAllTextFields });
```
```swift SwiftUI
UXCamCore.applyOcclusion(OccludeAllTextFields())
```
```csharp Xamarin
UXCam.applyOcclusion(new UXCamOccludeAllTextFields());
```

---

### Occlude Sensitive View

Hides specific UI elements containing sensitive data.

```java Android
UXCam.occludeSensitiveView(View sensitiveView);
```
```swift iOS
UXCam.occludeSensitiveView(sensitiveView)
```
```dart Flutter
OccludeWrapper(child: YourSensitiveWidget())
```
```javascript React Native
<Button ref={view => RNUxcam.occludeSensitiveView(view)} />
```
```swift SwiftUI
Text("Sensitive").uxcamOcclude()
Text("With gestures").uxcamOcclude(blockGestures: false)
```
```csharp Xamarin
UXCam.OccludeSensitiveView(sensitiveView);
```
```html Cordova
<input class="uxcam-occlude" type="text" />
```

---

### Configuration Object Setup

Apply multiple occlusions at SDK initialization:

```java Android
UXConfig config = new UXConfig.Builder(appKey)
    .occlusions(Arrays.asList(blur, overlay, textFields))
    .build();
UXCam.startWithConfiguration(config);
```
```swift iOS
let occlusion = UXCamOcclusion()
occlusion.apply(blurSetting, screens: ["LoginViewController"])
configuration.occlusion = occlusion
UXCam.start(with: configuration)
```
```dart Flutter
FlutterUxConfig config = FlutterUxConfig(userAppKey: "KEY", occlusions: [blur, overlay]);
FlutterUxcam.startWithConfiguration(config);
```
```javascript React Native
RNUxcam.startWithConfiguration({ userAppKey: 'KEY', occlusions: [overlay, blur] });
```

---

## Platform Implementation Guides

For detailed implementation guides with best practices and troubleshooting:

| Platform | Guide |
|----------|-------|
| Android | [Sensitive Data Occlusion](/docs/sensitive-data-occlusion) |
| iOS | [Sensitive Data Occlusion](/docs/sensitive-data-occlusion-ios) |
| Flutter | [Sensitive Data Occlusion](/docs/sensitive-data-occlusion-flutter) |
| React Native | [Sensitive Data Occlusion](/docs/sensitive-data-occlusion-react-native) |
| Cordova | [Sensitive Data Occlusion](/docs/sensitive-data-occlusion-cordova) |

---

## Occlusion Priority Order

When multiple rules apply, priority is (highest to lowest):

1. Dashboard: Screen-specific overlay
2. Dashboard: Screen-specific blur
3. Dashboard: Global blur/overlay
4. SDK: Screen-specific overlay
5. SDK: Screen-specific blur
6. SDK: Global blur/overlay
