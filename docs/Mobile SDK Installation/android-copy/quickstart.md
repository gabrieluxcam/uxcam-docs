---
title: 📱 Quick‑Start
excerpt: "Get replays, crashes, ANRs and heat‑maps in under 60\_minutes."
deprecated: false
hidden: false
metadata:
  robots: index
---
## 1 Prerequisites

* **minSdkVersion 21**
* **Java 8 / Kotlin 1.6+**
* Gradle Android Plugin 7.4+
* Internet permission (`android.permission.INTERNET`)

***

## 2 Install the SDK -> Link

```kotlin
repositories {
    maven { url = uri("https://sdk.uxcam.com/android/") }
}
dependencies {
    implementation("com.uxcam:uxcam:3.+")
}
```
```groovy
repositories {
    maven { url 'https://sdk.uxcam.com/android/' }
}
dependencies {
    implementation 'com.uxcam:uxcam:3.+'
}
```

> If you use ProGuard/R8, keep the `com.uxcam` package.

***

## 3 Initialise & record your first session -> Link

```kotlin
class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()
        val config = UXConfig.Builder(BuildConfig.UXCAM_KEY)
            .enableAutomaticScreenNameTagging(true)
            .build()
        UXCam.startWithConfiguration(config)
    }
}
```

Look for **“Verification successful”** in Logcat, interact with the app and then send it to background.

***

## 4 Mask PII (mandatory check) -> Link

Run a test session. If you can read passwords or card numbers in the replay, apply:

```kotlin
val payOverlay = UXCamOverlay.Builder()
    .screens(listOf("PaymentActivity"))
    .withoutGesture(true)
    .build()
UXCam.applyOcclusion(payOverlay)
```

More options → [Masking PII & Sensitive Content](#masking-pii-and-sensitive-content).

***

## 5 Tag screens -> Link

Automatic tags cover most apps.\
Manual example:

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    UXCam.tagScreenName("Checkout")
}
```

Jetpack Compose navigation listener snippet → [Screen Tagging](#screen-tagging).

***

## 6 Identify the user & set properties

```kotlin
UXCam.setUserIdentity(authResult.userId)
UXCam.setUserProperty("plan", "pro")
UXCam.setUserProperty("signup_source", "google_ads")
```

***

## 7 Log an event

```kotlin
val props = hashMapOf<String, Any>(
    "plan" to "pro",
    "price_cents" to 1499
)
UXCam.logEvent("Payment_Succeeded", props)
```

***

## 8 Verify checklist

* [ ] Session appears on dashboard within 2 min
* [ ] No PII visible in replay
* [ ] Screen names correct, no 0 s duplicates
* [ ] User appears under **Users → Search**
* [ ] Events display with properties

***

## Next steps

* **Crashes & ANRs** – auto‑captured, view stack trace + replay.
* **Advanced occlusion** – WebView & Compose helpers.
* **API Reference** – [Android SDK Method Docs](/reference/android).
* **Changelog** – [Release Notes](/android/changelog).