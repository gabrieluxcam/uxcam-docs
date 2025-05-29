---
title: Masking PII & Sensitive Content
excerpt: Protect your users’ privacy before the first session hits the dashboard.
deprecated: false
hidden: false
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

### Why this matters

Under GDPR / CCPA *you* are the data-controller.  
UXCam records screens so you can debug and improve UX—but **it should never receive raw Personally Identifiable Information (PII)** such as:

* passwords • access tokens  
* credit-card or IBAN numbers  
* government IDs • phone numbers • e-mail addresses  
* faces or documents shown in an OCR/KYC flow

The SDK lets you **mask or blur** that content **on the device, before the video is encoded**, so nothing sensitive ever leaves the user’s phone.

---

### 1  Pin-point what needs to be hidden

Run the app once with full UXCam recording enabled, and go through all user flows in your app. Then answer:

| UI element | Typical examples | Best occlusion scope |
|------------|------------------|----------------------|
| Single *field* holding PII | password box, card number input | **Sensitive `View`** |
| A *panel* with mixed info | address block, invoice rows | **Overlay / Blur** on specific *screens* |
| A *full screen* of PII | full checkout / ID-verification screen | **Global Overlay / Blur** for that screen |

> ℹ️ **Gesture capture**: Overlays and blurs can keep or hide touch dots. If the user might type a password, disable gesture capture too.

---

### 2  Pick an SDK helper

| Helper class | Masks … | Keeps gestures? | When to choose |
|--------------|---------|-----------------|----------------|
| `UXCamOverlay` | Solid colour (red by default) | Optional | You must *fully* cover PII and don’t care about screen context |
| `UXCamBlur` | Adjustable Gaussian blur (1-25) | Optional | You need to see layout & movement, but not the actual data |
| `UXCamOccludeAllTextFields` | Every `EditText` on chosen screens | Always keeps | Quick win if all PII sits in text fields |
| `UXCam.occludeSensitiveView(view)` | One specific `View` | Inherits | Granular—ideal for a single card-number box |

All helpers share two powerful parameters:

```java
screens(List<String> screenNames)     // limit to certain screens
excludeMentionedScreens(boolean)      // treat list as allow-list (false) or deny-list (true)
````

---

### 3  Step-by-step examples

> Each snippet is self-contained—copy it into **`onCreate()` of your `Application`** or the target `Activity`.

#### 3.1  Hide one payment screen completely

```java
// 1) Build an overlay that hides touches too
UXCamOverlay payOverlay = new UXCamOverlay.Builder()
        .screens(Collections.singletonList("PaymentActivity"))
        .withoutGesture(true)
        .build();

// 2) Apply it
UXCam.applyOcclusion(payOverlay);
```

*Effect:* Every frame captured inside `PaymentActivity` is replaced by a red plate.
Touches are suppressed, guarding against key-logging.

---

#### 3.2  Blur everything **except** Settings

```java
UXCamBlur globalBlur = new UXCamBlur.Builder()
        .excludeMentionedScreens(true)           // treat list below as “allow video”
        .screens(Collections.singletonList("SettingsActivity"))
        .blurRadius(12)                          // medium blur
        .build();

UXCam.applyOcclusion(globalBlur);
```

*Effect:* All screens are blurred, but `SettingsActivity` remains crystal-clear—ideal when only one screen is non-sensitive.

---

#### 3.3  Occlude **all text fields** on Checkout + Login

```java
UXCamOccludeAllTextFields hideText = new UXCamOccludeAllTextFields.Builder()
        .screens(Arrays.asList("CheckoutActivity", "LoginActivity"))
        .build();

UXCam.applyOcclusion(hideText);
```

*Effect:* Every `EditText` (even future ones) on the listed screens is boxed—no extra code per field.

---

#### 3.4  Mask a single `EditText`

```java
EditText cardInput = findViewById(R.id.cardNumberInput);
UXCam.occludeSensitiveView(cardInput);
```

*Effect:* Only the card box is hidden; the rest of the screen is visible, so you can still diagnose flow problems.

---

### 4  Apply occlusions automatically at startup

Put multiple rules in one list and pass them to the *initial* configuration:

```java
UXConfig config = new UXConfig.Builder(BuildConfig.UXCAM_KEY)
        .occlusions(Arrays.asList(globalBlur, hideText))  // from examples above
        .build();

UXCam.startWithConfiguration(config);
```

---

### 5  Defaults you get for free

* Android `EditText` with `android:inputType="textPassword"` or
  `InputType.TYPE_TEXT_VARIATION_PASSWORD` is auto-occluded.
* **Jetpack Compose** does **not** auto-occlude—use the KTX helper below.

---

## 🛠️ Jetpack Compose quick-start

1. Add the Kotlin extensions:

   ```gradle
   implementation("com.uxcam:uxcam-ktx:1.+")
   ```

2. Hide a Composable:

   ```kotlin
   @Composable
   fun SecureField(password: String) {
       val view = LocalView.current

       TextField(
           value = password,
           onValueChange = { /*…*/ },
           modifier = Modifier.onGloballyPositioned { pos ->
               UXCamKt.occludeSensitiveComposable(
                   identifier = "pwdBox",
                   view = view,
                   coordinates = pos          // position on screen
               )
           }
       )
   }
   ```

*The identifier must be unique per screen; `isInDialog=true` if that composable sits inside a `Dialog`.*

---

### Dashboard-only rules (no code)

Prefer a zero-code workflow?
Open **App Settings → Video Recording Privacy** on the UXCam Dashboard:

* **Blur / occlude all screens** in one click.
* Add **screen-specific rules** (e.g., blur Checkout, hide Login).
* Toggle **“Record gestures on blurred screens”** if needed.

Dashboard rules override SDK calls in this order:

1. Screen-specific *overlay* (dashboard)
2. Screen-specific *blur* (dashboard)
3. Global overlay / blur (dashboard)
4. Screen-specific overlay / blur (SDK)
5. Global overlay / blur (SDK)

---

✅ **Next step:** replay a test session to confirm every PII element is masked or blurred before pushing to production.