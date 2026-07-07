---
title: Understanding Occlusion
excerpt: How to protect sensitive data in session recordings
deprecated: false
hidden: false
metadata:
  title: 'Understanding Occlusion - UXCam'
  description: 'Learn the different strategies for hiding sensitive data in UXCam recordings'
  robots: index
next:
  description: ''
---

# Understanding Occlusion

Occlusion is the process of hiding sensitive data in session recordings. UXCam provides multiple occlusion strategies to protect user privacy while maintaining analytical value.

<OcclusionDemo />

---

## Occlusion Strategies

### 1. View-Level Occlusion

Hide specific UI elements in recordings.

```
┌─────────────────────────┐      ┌─────────────────────────┐
│  Name: John Doe         │  →   │  Name: John Doe         │
│  Card: 4111-1111-1111   │      │  Card: █████████████    │
│  [Submit]               │      │  [Submit]               │
└─────────────────────────┘      └─────────────────────────┘
       Original                        Occluded
```

**Use for:**
- Credit card fields
- Personal ID numbers
- Medical information
- Any specific sensitive input

### 2. Screen-Level Occlusion

Hide everything on specific screens.

```
┌─────────────────────────┐      ┌─────────────────────────┐
│  Account Settings       │  →   │                         │
│  SSN: 123-45-6789      │      │   Screen Occluded       │
│  Bank: Chase ****4321   │      │                         │
│  [Save]                 │      │                         │
└─────────────────────────┘      └─────────────────────────┘
       Original                        Occluded
```

**Use for:**
- Payment screens
- Settings with sensitive data
- Profile pages with PII

### 3. Blur Occlusion

Blur rather than completely hide content. Preserves layout visibility.

```
┌─────────────────────────┐      ┌─────────────────────────┐
│  Name: John Doe         │  →   │  Name: ░░░░░░░░░        │
│  Email: john@email.com  │      │  Email: ░░░░░░░░░░░░░   │
│  [Edit Profile]         │      │  [Edit Profile]         │
└─────────────────────────┘      └─────────────────────────┘
       Original                        Blurred
```

**Use for:**
- Data that needs layout visibility
- Less sensitive but still private content
- A/B testing different occlusion approaches

### 4. Overlay Occlusion

Place a colored rectangle over sensitive areas.

```
┌─────────────────────────┐      ┌─────────────────────────┐
│  Chat with Support      │  →   │  Chat with Support      │
│  ┌───────────────────┐  │      │  ┌███████████████████┐  │
│  │ Here's my address │  │      │  │                   │  │
│  │ 123 Main St...    │  │      │  │                   │  │
│  └───────────────────┘  │      │  └███████████████████┘  │
└─────────────────────────┘      └─────────────────────────┘
       Original                      Overlay Applied
```

**Use for:**
- Chat/messaging content
- User-generated content areas
- Dynamic content regions

---

## Implementation by Platform

### Android

```kotlin
import com.uxcam.UXCam

// Single view
UXCam.occludeSensitiveView(sensitiveView)

// Remove occlusion
UXCam.unoccludeSensitiveView(sensitiveView)

// Entire screen
UXCam.occludeSensitiveScreen(true)

// With blur (instead of solid)
UXCam.occludeSensitiveViewWithoutGesture(view)
```

**Jetpack Compose:**

```kotlin
import com.uxcam.compose.UXCamOcclude

@Composable
fun PaymentScreen() {
    UXCamOcclude {
        // Everything in here is occluded
        CreditCardInput()
    }
}
```

### iOS

```swift
import UXCam

// Single view
UXCam.occlude(sensitiveView)

// With blur
UXCam.occlude(sensitiveView, withBlur: true)

// Remove occlusion
UXCam.unocclude(sensitiveView)

// All text fields
UXCam.occludeAllTextFields(true)
```

**SwiftUI:**

```swift
import UXCamSwiftUI

var body: some View {
    VStack {
        Text("Payment")
        CreditCardField()
            .uxcamOcclude()  // Modifier to occlude
    }
}
```

### React Native

```javascript
import RNUxcam from 'react-native-ux-cam';
import { UXCamOccludedView } from 'react-native-ux-cam';

// Using wrapper component
<UXCamOccludedView>
  <CreditCardInput />
</UXCamOccludedView>

// Screen-level
RNUxcam.occludeSensitiveScreen(true);

// View ref-based
const viewRef = useRef(null);
RNUxcam.occludeSensitiveView(viewRef);
```

### Flutter

```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

// Using wrapper widget
UxcamOccludeWrapper(
  child: CreditCardInput(),
)

// Screen-level
FlutterUxcam.occludeSensitiveScreen(true);

// By widget key
FlutterUxcam.occludeSensitiveView(sensitiveViewKey);
```

### Web

```javascript
// By element
uxcam('occlude', document.getElementById('credit-card'));

// By CSS selector
uxcam('occlude', '.sensitive-data');

// Remove occlusion
uxcam('unocclude', document.getElementById('credit-card'));
```

---

## Automatic Occlusion

UXCam automatically occludes common sensitive fields:

| Field Type | Auto-Occluded |
|------------|---------------|
| Password inputs | Yes |
| Credit card fields (detected) | Yes |
| SSN/Tax ID fields (detected) | Yes |
| Generic text inputs | **No** - manual required |
| Labels/text views | **No** - manual required |

<GitHubCallout type="warning">Don't rely solely on automatic occlusion. Always test your app's recordings to verify sensitive data is properly hidden.</GitHubCallout>

---

## Configuration at SDK Start

You can configure occlusion rules when starting the SDK:

### Android

```kotlin
val occlusion = UXCamOcclusion.Builder()
    .withoutGesture(true)  // Also hide touch indicators
    .screens(listOf(
        UXCamOccludeAllTextFields.occludeAllTextFields()
    ))
    .build()

val config = UXConfig.Builder("YOUR_KEY")
    .occlusions(listOf(occlusion))
    .build()

UXCam.startWithConfiguration(config)
```

### iOS

```swift
let config = UXCamConfiguration(appKey: "YOUR_KEY")
config.occlusion = UXCamOcclusion.occludeAllTextFields()
UXCam.start(with: config)
```

---

## WebView Occlusion

WebViews require special handling since they render web content:

### Android WebView

```kotlin
// Occlude entire WebView
UXCam.occludeSensitiveView(webView)

// Or inject CSS for selective hiding
webView.evaluateJavascript("""
    document.querySelectorAll('.sensitive').forEach(el => {
        el.style.filter = 'blur(10px)';
    });
""", null)
```

### iOS WKWebView

```swift
// Occlude entire WebView
UXCam.occlude(webView)

// CSS injection approach
let css = ".sensitive { filter: blur(10px); }"
let js = "var style = document.createElement('style'); style.innerHTML = '\(css)'; document.head.appendChild(style);"
webView.evaluateJavaScript(js)
```

---

## Testing Occlusion

Always verify occlusion works before production:

1. **Enable debug logging** to see occlusion events
2. **Record a test session** with sample data
3. **Review in dashboard** - check recordings for any visible sensitive data
4. **Test edge cases:**
   - Dynamically added views
   - Views that appear after navigation
   - Content loaded from network

### Debug Logging

```kotlin
// Android
val config = UXConfig.Builder("YOUR_KEY")
    .enableIntegrationLogging(true)
    .build()
```

```swift
// iOS
config.enableIntegrationLogging = true
```

Look for logs like:
```
UXCam: Occluded view: CreditCardInput
UXCam: Screen occlusion enabled for: PaymentScreen
```

---

## Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Data still visible | Occlusion applied too late | Apply occlusion in `viewDidLoad`/`onCreate` |
| Wrong element occluded | Incorrect view reference | Verify view hierarchy |
| Occlusion not working in WebView | Web content not accessible | Use CSS injection or occlude entire WebView |
| Dynamic content not occluded | View added after occlusion call | Re-apply occlusion or use parent container |

---

## Further Reading

- [Privacy and Compliance](privacy-and-compliance) - GDPR/CCPA requirements
- [Sensitive Data Occlusion - Android](/docs/sensitive-data-occlusion) - Platform-specific guide
- [Sensitive Data Occlusion - iOS](/docs/sensitive-data-occlusion-ios) - Platform-specific guide
