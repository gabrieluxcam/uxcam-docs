---
title: Android SDK Integration Guide
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
<GitHubCallout type="note">Need an account? \*\*[Start free](/signup)       \*\* – most teams finish this setup in **\< 15 min**.</GitHubCallout>

<JumpCallout to="#android-sdk--quickstart">Skip to Quickstart</JumpCallout>

<Accordion title="Key Benefits After Setup" icon="fa-info-circle">
  * **Session Replay + Heat-maps** – watch every UX moment in context.
  * **Advanced Product Analytics** – funnels, retention, feature adoption.
  * **Insight Alerts** – journey summaries, rage-tap & anomaly detection.
  * **Engineering Analytics** – client-side performance tied to real sessions, monitoring crashes, ANRs and handled exceptions with session replay & developer logs for faster debugging.
</Accordion>

<Accordion title="Tips Before You Begin" icon="fa-lightbulb-o">
  * Create **separate keys** for *debug* & *production* to keep data clean.
  * Use **feature flags** to toggle UXCam in staging builds.
  * Check Logcat for **“Verification successful”** and **“Session/Video uploaded”** messages to confirm everything is wired up.
</Accordion>

# Android SDK · Quickstart

## Step 1: Add the SDK

[![pod version](https://img.shields.io/badge/Maven-3.+-green)](#)

In your module's **build.gradle**, add:

```groovy build.gradle (app)
repositories {
  maven { url 'https://sdk.uxcam.com/android/' }
}
dependencies {
  implementation 'com.uxcam:uxcam:3.+'
}
```

## Step 2: Start Recording

From your Application Class or Launcher Activity, add the configuration and start the SDK.

<GitHubCallout type="important">Make sure to start the SDK at the earliest possible Context.</GitHubCallout>

```kotlin Kotlin
import com.uxcam.UXCam
import com.uxcam.datamodel.UXConfig

class MyApp : Application() {
  override fun onCreate() {
    super.onCreate()
    val config = UXConfig.Builder(BuildConfig.YOUR_UXCAM_KEY).build()
    UXCam.startWithConfiguration(config)
}`
```
```java Java
import com.uxcam.UXCam;
import com.uxcam.datamodel.UXConfig;


public class MyApp extends Application { 
@Override
public void onCreate() {
super.onCreate();
UXConfig config = new UXConfig.Builder(BuildConfig.YOUR_UXCAM_KEY).build();
UXCam.startWithConfiguration(config);
 }
}
```

<GitHubCallout type="success">Watch Logcat for **“Verification successful”** – your first session should appear on the UXCam dashboard within \~2 minutes.</GitHubCallout>