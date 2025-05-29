---
title: 'Occlude sensitive data '
deprecated: false
hidden: true
metadata:
  robots: index
next:
  description: >-
    Excellent! You can record some test sessions and once you're finalised with
    occlusion, we can move onto custom events and user properties!
  pages:
    - slug: users-and-properties
      title: Users and Properties
      type: basic
---
## 🔒 Occluding Sensitive Data

### Why it matters  
**Personally Identifiable Information (PII)** is any data that can single-out a user—e.g. passwords, credit-card numbers, e-mail, phone, government IDs, faces in images.  
UXCam never *needs* that content, so make sure it’s hidden **before your first production release**.

---

### 1  Identify what to hide

| PII in your UI | Occlusion scope you’ll need |
|----------------|-----------------------------|
| Login / card-number `EditText` | **Text fields** only |
| A checkout block or profile panel | **Section of the screen** |
| Entire payment / KYC screen | **Whole screen** |

> 🔍 Tip: open each screen once in Debug + UXCam logs. Anything **not** obscured will show up in the video.

---

### 2  Choose an SDK helper

| Helper | Hides… | Gestures? | Sample call |
|--------|--------|-----------|-------------|
| **`UXCamOverlay`** | Solid colour box over screen(s) | Optional | `UXCam.applyOcclusion(overlay)` |
| **`UXCamBlur`** | Gaussian blur (radius 1-25) | Optional | `UXCam.applyOcclusion(blur)` |
| **`UXCamOccludeAllTextFields`** | Every `EditText` on chosen screens | Always kept | `UXCam.applyOcclusion(textFields)` |
| **`occludeSensitiveView(view)`** | One specific `View` | Inherited from parent | `UXCam.occludeSensitiveView(passwordEdit)` |

---

### 3  Code snippets

#### 3.1 Hide a complete payment screen

```java
UXCamOverlay payOverlay = new UXCamOverlay.Builder()
        .screens(Collections.singletonList("PaymentActivity"))
        .withoutGesture(true)      // hide taps too
        .build();

UXCam.applyOcclusion(payOverlay);
````

#### 3.2 Blur everything *except* Settings

```java
UXCamBlur globalBlur = new UXCamBlur.Builder()
        .excludeMentionedScreens(true)           // “blacklist” mode
        .screens(Collections.singletonList("SettingsActivity"))
        .blurRadius(12)
        .build();

UXCam.applyOcclusion(globalBlur);
```

#### 3.3 Occlude all text fields on Checkout + Login

```java
UXCamOccludeAllTextFields hideText = new UXCamOccludeAllTextFields.Builder()
        .screens(Arrays.asList("CheckoutActivity", "LoginActivity"))
        .build();

UXCam.applyOcclusion(hideText);
```

#### 3.4 Hide one sensitive `EditText`

```java
UXCam.occludeSensitiveView(findViewById(R.id.cardNumberInput));
```

---

### 4  Add occlusions at startup (optional)

```java
UXConfig config = new UXConfig.Builder(BuildConfig.UXCAM_KEY)
        .occlusions(Arrays.asList(globalBlur, hideText))
        .build();

UXCam.startWithConfiguration(config);
```

---

### 5  Default protection

* Android `EditText` with `android:inputType="textPassword"` and
  `InputType.TYPE_TEXT_VARIATION_PASSWORD` are **auto-occluded**.
* Jetpack Compose does not auto-occlude—use the KTX helper below.

---

## 🛠️ Jetpack Compose quick-start

Add **`uxcam-ktx`**:

```gradle
implementation("com.uxcam:uxcam-ktx:1.+")
```

Hide a single composable:

```kotlin
@Composable
fun SecureField() {
    val view = LocalView.current
    TextField(
        value = password,
        onValueChange = { … },
        modifier = Modifier.onGloballyPositioned { pos ->
            UXCamKt.occludeSensitiveComposable(
                identifier = "pwd",
                view = view,
                coordinates = pos
            )
        }
    )
}
```

---

### Need dashboard-side rules?

Most teams stick to SDK calls. If you prefer *no-code* occlusions, open
**App Settings → Video Recording Privacy** on the UXCam dashboard and add rules for *all screens* or *specific screens*.

---

**That’s it!** Verify by replaying a test session: all PII should be masked or blurred.
