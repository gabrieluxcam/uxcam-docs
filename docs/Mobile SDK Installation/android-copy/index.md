---
title: Android
excerpt: How to Get Started with UXCam for Android
deprecated: false
hidden: false
metadata:
  robots: index
next:
  pages:
    - slug: initialise-uxcam-and-start-recording
      title: Initialise UXCam SDK and start recording
      type: basic
---
# 📱 Android Quick-Start

Welcome! If you haven’t signed up yet, grab your **free trial** → [Sign Up](https://app.uxcam.com/signup).

Once your account is ready, follow the five-step path below.  
Most teams complete the core setup in **about two hours** with a single developer.

---

## Integration Journey at a Glance

<Table align={["left","left","left"]}>
<thead>
<tr><th>#</th><th>Step</th><th>Goal</th></tr>
</thead>
<tbody>

<tr>
<td><strong>1</strong></td>
<td><strong>Initialise SDK & Start Recording</strong> <a href="#1-initialise-sdk--start-recording">Jump →</a></td>
<td>Capture your first live session</td>
</tr>

<tr>
<td><strong>2</strong></td>
<td><strong>Mask PII & Sensitive Content</strong> <a href="#2-mask-pii--sensitive-content">Jump →</a></td>
<td>Hide passwords and other GDPR/CCPA data</td>
</tr>

<tr>
<td><strong>3</strong></td>
<td><strong>Tag Screens</strong> <a href="#3-tag-screens">Jump →</a></td>
<td>Enable heat-maps & screen analytics</td>
</tr>

<tr>
<td><strong>4 ★</strong></td>
<td><strong>Set User Identity & Properties</strong> <a href="#4-set-user-identity--properties">Jump →</a></td>
<td>Unify sessions, power funnels & cohorts</td>
</tr>

<tr>
<td><strong>5 ★</strong></td>
<td><strong>Tag Events</strong> <a href="#5-tag-events">Jump →</a></td>
<td>Measure key actions & run final QA</td>
</tr>

</tbody>
</Table>

> ★ **Optional but highly recommended.** Ship steps 1-3 to start getting replays and heat-maps, then add steps 4-5 for deeper analytics.

---

## Key Benefits After Setup

* **Session Replay + Heat-maps** – watch every UX moment in context.  
* **Advanced Product Analytics** – funnels, retention, feature adoption.  
* **Insight Alerts** – journey summaries, rage-tap & anomaly detection.  
* **Engineering Analytics** – client-side performance tied to real sessions, monitoring crashes, ANRs and handled exceptions with session replay & developer logs for faster debugging.

---

## 💡 Tips Before You Begin

* Create **separate keys** for *debug* & *production* to keep data clean.  
* Use **feature flags** to toggle UXCam in staging builds.  
* Check Logcat for **“Verification successful”** and **“Session/Video uploaded”** messages to confirm everything is wired up.

Happy analysing! 🎉

---

## 1 – Initialise SDK & Start Recording
### 1.1 Prerequisites
* **minSdkVersion 21**
* **Java 8 / Kotlin 1.6+**
* Gradle Android Plugin 7.4+
* `android.permission.INTERNET`

### 1.2 Add the dependency

```kotlin
repositories { maven { url = uri("https://sdk.uxcam.com/android/") } }
dependencies { implementation("com.uxcam:uxcam:3.+") }
````
```groovy
repositories { maven { url 'https://sdk.uxcam.com/android/' } }
dependencies { implementation 'com.uxcam:uxcam:3.+' }
```

### 1.3 Start the SDK

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

Look for **“Verification successful”** in Logcat.

---

## 2 – Mask PII & Sensitive Content

Run a test session. If you spot passwords/card numbers in the replay, mask them:

```kotlin
val payOverlay = UXCamOverlay.Builder()
    .screens(listOf("PaymentActivity"))
    .withoutGesture(true)
    .build()
UXCam.applyOcclusion(payOverlay)
```

More options → see the *Masking PII* guide.

---

## 3 – Tag Screens

Automatic tags usually suffice. Manual example:

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
  super.onCreate(savedInstanceState)
  UXCam.tagScreenName("Checkout")
}
```

For Jetpack-Compose navigation, use a `NavController` listener (see full guide).

---

## 4 – Set User Identity & Properties

```kotlin
UXCam.setUserIdentity(authResult.userId)
UXCam.setUserProperty("plan", "pro")
UXCam.setUserProperty("signup_source", "google_ads")
```

---

## 5 – Tag Events

```kotlin
val props = hashMapOf("plan" to "pro", "price_cents" to 1499)
UXCam.logEvent("Payment_Succeeded", props)
```

---

## Verification Checklist

* [ ] Session appears on dashboard within 2 min
* [ ] No PII visible in replay
* [ ] Screen names correct, no 0 s duplicates
* [ ] User profile populated with ID & properties
* [ ] Events display with properties

---

### Next Steps

* **Crashes & ANRs** – auto-captured; inspect stack trace + replay.
* **Advanced occlusion** – WebView & Compose helpers.
* **API Reference** – [Android SDK docs](/reference/android).
* **Changelog** – [Release notes](/android/changelog).
