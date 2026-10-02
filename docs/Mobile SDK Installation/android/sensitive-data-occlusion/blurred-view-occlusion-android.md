---
title: Blurred View Occlusion
excerpt: Blur occluded views instead of covering them with a solid rectangle
deprecated: false
hidden: true
metadata:
  title: Blurred View Occlusion - UXCam Android SDK
  description: Render view-level occlusions as a blur of the view's own pixels instead of a solid rectangle, per view or session-wide, on the UXCam Android SDK.
  robots: index
next:
  description: ''
---
> 📘 Availability
>
> Android SDK **v3.12.0 and above** (native Views and Jetpack Compose). WebView, Flutter and React Native view occlusion keep the solid style for now.

UXCam hides sensitive views in session replays by covering them with a solid rectangle. **Blurred view occlusion** adds a second rendering style: instead of a solid box, the view's own pixels are blurred beyond recognition. Replays keep their visual context — you can still see *that* there is a card preview or a form there, and how the user interacts around it — while the content itself stays unreadable.

Blurring changes only how occluded regions **look**. What gets occluded, gesture handling and the privacy guarantee are identical to standard occlusion: everything is processed **on the device, before the video is encoded**, so sensitive content never leaves the phone readable.

> ℹ️ Not the same as `UXCamBlur`
>
> `UXCamBlur` blurs **whole screens**. Blurred view occlusion applies to the **individual views** you register with `occludeSensitiveView` (and to auto-detected fields such as password inputs). Both can be used together.

---

## Blur every occluded view

Switch the whole session to blurred rendering with one option at start. Every view-level occlusion — views you mark manually, auto-detected sensitive fields such as password inputs, `UXCamOccludeAllTextFields`, and Jetpack Compose occlusion — renders blurred instead of boxed:

```kotlin Kotlin
val config = UXConfig.Builder(APP_KEY)
    .sensitiveViewOcclusionStyle(ViewOcclusionStyle.BLUR)
    .build()
UXCam.startWithConfiguration(config)
```
```java Java
UXConfig config = new UXConfig.Builder(APP_KEY)
    .sensitiveViewOcclusionStyle(ViewOcclusionStyle.BLUR)
    .build();
UXCam.startWithConfiguration(config);
```

The default style is `ViewOcclusionStyle.OVERLAY` — the classic solid rectangle. Without this option the SDK behaves exactly as before.

---

## Blur a specific view

To choose the style per view, pass a `ViewOcclusionStyle` to `occludeSensitiveView`:

```kotlin Kotlin
UXCam.occludeSensitiveView(ssnField)                               // session default (solid unless configured)
UXCam.occludeSensitiveView(cardPreview, ViewOcclusionStyle.BLUR)   // blurred
UXCam.occludeSensitiveView(avatar, ViewOcclusionStyle.OVERLAY)     // solid, even if the session default is BLUR

// Blur and also ignore gestures that start on the view:
UXCam.occludeSensitiveViewWithoutGesture(cardPreview, ViewOcclusionStyle.BLUR)
```
```java Java
UXCam.occludeSensitiveView(ssnField);                              // session default (solid unless configured)
UXCam.occludeSensitiveView(cardPreview, ViewOcclusionStyle.BLUR);  // blurred
UXCam.occludeSensitiveView(avatar, ViewOcclusionStyle.OVERLAY);    // solid, even if the session default is BLUR

// Blur and also ignore gestures that start on the view:
UXCam.occludeSensitiveViewWithoutGesture(cardPreview, ViewOcclusionStyle.BLUR);
```

The style is part of the same occlusion registration, so everything else works the same:

- A per-view style always overrides the session-wide default; plain `occludeSensitiveView(view)` follows the session default.
- Remove the occlusion with `UXCam.unOccludeSensitiveView(view)`.
- Manual registrations take precedence over auto-detection.
- Registering the same view again replaces the style — the last call wins.
- Gestures on the view are still tracked unless you use the `WithoutGesture` variant.

### `ViewOcclusionStyle` values

| Value | Rendering | Use when |
| --- | --- | --- |
| `DEFAULT` | Follows the session-wide style (what plain `occludeSensitiveView(view)` registers) | You want one place to decide the look for all views |
| `OVERLAY` | Solid rectangle — the classic occlusion | Even the shape or colour of the content is sensitive |
| `BLUR` | The view's own pixels blurred beyond recognition | You need layout and interaction context, not the data |

---

## Blur strength

The blur radius is set once, in **dp**, and applies to all blurred view occlusions:

```kotlin Kotlin
val config = UXConfig.Builder(APP_KEY)
    .sensitiveViewOcclusionStyle(ViewOcclusionStyle.BLUR)
    .sensitiveViewBlurRadius(25)   // default 25 dp
    .build()
```
```java Java
UXConfig config = new UXConfig.Builder(APP_KEY)
    .sensitiveViewOcclusionStyle(ViewOcclusionStyle.BLUR)
    .sensitiveViewBlurRadius(25)   // default 25 dp
    .build();
```

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `sensitiveViewOcclusionStyle` | `ViewOcclusionStyle` | `OVERLAY` | Session-wide rendering for view-level occlusions |
| `sensitiveViewBlurRadius` | Integer (dp) | `25` | Blur strength for `BLUR`. Values below **10** are clamped up to 10 |

> ⚠️ Minimum radius
>
> Values below **10 dp are clamped up to 10 dp**: a weaker blur can leave large text readable, which would defeat the occlusion. There is no upper limit — larger radii blur harder at no extra performance cost, because the SDK downscales the region before blurring.

---

## How it behaves

- **Fail-safe.** If a frame cannot be blurred on the device (for example, no working blur backend), that frame is covered with the standard solid rectangle instead. Sensitive content is never exposed. If blurring keeps failing, the SDK stops attempting it for the rest of the session and uses solid rectangles throughout.
- **Screen transitions.** Occluded views stay blurred while a screen navigation is in flight — there is no flash of the solid rectangle between screens.
- **Auto-detected fields.** With the session-wide style set to `BLUR`, fields the SDK detects on its own (password inputs, and `UXCamOccludeAllTextFields` when enabled) blur too — no code changes needed for them.
- **Jetpack Compose.** Composables occluded through `UXCamKt.occludeSensitiveComposable` follow the session-wide style. There is no per-composable style yet.
- **Gestures.** Unchanged from standard occlusion: tracked by default, blocked with the `WithoutGesture` variants.

---

## What stays solid

Blurred view occlusion covers **view-level** occlusion on native Android Views and Jetpack Compose. The following keep their existing rendering:

| Surface | Rendering |
| --- | --- |
| Full-screen occlusion (`UXCamOverlay`) | Opaque overlay, as configured |
| Full-screen blur (`UXCamBlur`) | Whole-screen blur, as before |
| WebView content occlusion | Solid rectangles |
| Flutter / React Native occlusion rects | Solid rectangles |
| Screens with `FLAG_SECURE` | Black frame |

Dashboard rules (**App Settings → Video Recording Privacy**) are unaffected and keep their [priority order](https://developer.uxcam.com/docs/sensitive-data-occlusion#dashboard-only-rules-no-code) over SDK calls.

---

## FAQ

**Is a blur as safe as a solid rectangle?**
At the enforced minimum radius, text and numbers are destroyed beyond recovery in the recorded video. For maximum redaction — where even the shape or colour of the content is sensitive — prefer `OVERLAY` or a full-screen `UXCamOverlay`.

**Does blurring cost more than the rectangle?**
Blurring does more work per frame than painting a rectangle, but regions are downscaled before blurring and deduplicated per frame, and the cost only applies to frames that actually contain blurred views. If blurring is unavailable on a device, the SDK falls back to rectangles automatically.

**Can I set a different radius per view?**
No — the radius is one session-wide setting. Per-view radii are not supported.

**Which SDKs support this?**
Android native (Views and Jetpack Compose occlusion) from v3.12.0. WebView, Flutter and React Native view occlusion keep the solid style for now.

---

**Verify:** replay a test session and confirm every blurred element is unreadable before pushing to production.
