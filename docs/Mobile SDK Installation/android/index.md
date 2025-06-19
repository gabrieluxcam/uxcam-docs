---
title: Android SDK Integration Guide
excerpt: How to Get Started with UXCam for Android
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: screen-tagging
      title: Screen Tagging
---
<GitHubCallout type="note">Need an account? \*\*[Start free](/signup) \*\* – most teams complete this setup in **under 15 minutes**..</GitHubCallout>

<JumpCallout to="#android-sdk--quickstart">Jump to Quickstart</JumpCallout>

# Integration Journey at a Glance

| Step                 | Task                                                                                                     | Goal                                    |
| :------------------- | :------------------------------------------------------------------------------------------------------- | :-------------------------------------- |
| <strong>1</strong>   | <strong>Customize SDK Configuration </strong>  <a href="#1-initialise-sdk--start-recording">Jump →</a>   | Capture your first live session         |
| <strong>3</strong>   | <strong>Tag Screens </strong>  <a href="#3-tag-screens">Jump →</a>                                       | Enable heat-maps & screen analytics     |
| <strong>2</strong>   | <strong>Mask Sensitive Data</strong>  <a href="#2-mask-pii--sensitive-content">Jump →</a>                | Hide passwords and other GDPR/CCPA data |
| <strong>4 ★</strong> | <strong>Identify Users & Set Properties </strong>  <a href="#4-set-user-identity--properties">Jump →</a> | Unify sessions, power funnels & cohorts |
| <strong>5 ★</strong> | <strong>Track Custom Events</strong>  <a href="#5-tag-events">Jump →</a>                                 | Measure key actions & run final QA      |

<GitHubCallout type="tip"> ★ **Optional but highly recommended.** Ship steps 1-3 to start getting session replays and heatmaps, then add steps 4-5 for deeper analytics.</GitHubCallout>

<SimpleStepper>
  <SimpleStep header="Step 1: Customize SDK Configuration">
    Capture your first live session.
  </SimpleStep>

  <SimpleStep header="Step 2: Tag Screens">
    Enable heat-maps & screen analytics.
  </SimpleStep>

  <SimpleStep header="Step 3: Mask Sensitive Data">
    Hide passwords and other GDPR/CCPA data.
  </SimpleStep>

  <SimpleStep header="Step 4: Identify Users & Set Properties">
    Unify sessions, power funnels & cohorts
  </SimpleStep>

  <SimpleStep header="Step 5: Track Custom Events">
    Measure key actions & run final QA
  </SimpleStep>
</SimpleStepper>

<Accordion title="Key Benefits After Setup" icon="fa-info-circle">
  * **Session Replay + Heatmaps** – Watch every user experience moment in full context.
  * **Advanced Product Analytics** – funnels, retention, feature adoption.
  * **Insight Alerts** – journey summaries, rage-tap & anomaly detection.
  * **Engineering Analytics** – Connect client-side performance to real sessions. Monitor crashes, ANRs, and handled exceptions with session replay and developer logs for faster debugging.
</Accordion>

<Accordion title="Tips Before You Begin" icon="fa-bolt">
  * Create **separate keys** for *debug* & *production* to keep data clean.
  * Use **feature flags** to toggle UXCam in staging builds.
  * Check Logcat for **“Verification successful”** and **“Session/Video uploaded”** messages to confirm the integration is working correctly.
</Accordion>

***

# Android SDK · Quickstart

## Step 1: Get your App Key

### 1.1: Find your app key

If you don't already have it, find it in App Settings -> App Key on your <a href="https://app.uxcam.com" target="_blank" rel="noopener">UXCam Dashboard</a>.

<GitHubCallout type="important">**Create separate keys for debug/production.** Keep your data clean, no need to mix test actions with real user insights!</GitHubCallout>

### 1.2: Keep it out of source control:

add it to `local.properties` (already in the default `.gitignore`) or inject it via your CI, then expose it safely at compile-time and avoid hard-coding secrets in your codebase.

***

## Step 2: Add the SDK Dependency

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

***

## Step 3: Start Recording

From your Application Class or Launcher Activity, add the configuration and start the SDK.

<GitHubCallout type="important">Make sure to initialize the SDK from the earliest possible Context.</GitHubCallout>

```kotlin Kotlin
import com.uxcam.UXCam
import com.uxcam.datamodel.UXConfig

class MyApp : Application() {
  override fun onCreate() {
    super.onCreate()
    val config = UXConfig.Builder(YOUR_UXCAM_KEY).build()
    UXCam.startWithConfiguration(config)
}
```
```java Java
import com.uxcam.UXCam;
import com.uxcam.datamodel.UXConfig;


public class MyApp extends Application { 
@Override
public void onCreate() {
super.onCreate();
UXConfig config = new UXConfig.Builder(YOUR_UXCAM_KEY).build();
UXCam.startWithConfiguration(config);
 }
}
```

<GitHubCallout type="success">This completes the most basic integration process.                     Your first session recording will appear on your dashboard a few minutes after the app is sent to the background.</GitHubCallout>

***

# Advanced SDK Configuration

You can control how the SDK is configured via the configuration object you create, the available parameters are below:

## Enable Debug Logs?

Set our configuration option `.enableIntegrationLogging(bool)` as `true` to see detailed UXCam logs in Logcat. This is useful for verifying your setup and troubleshooting any issues.

## Other Configuration Options

* `.enableAutomaticScreenNameTagging(bool)`  True by Default. Set to false if you want to disable automatic screen tagging.
* `.occlusions(Arrays.asList(occlusion))` For passing occlusion for screens from the SDK startup, we'll get to that [here]().
* `.enableCrashHandling(bool)` True by Default. Set to false if you want to disable crash capture.
* `.enableMultiSessionRecord(bool)` True by Default. Set to false to stop any session after the first one from being recorded.

***

# Next Steps

You've successfully integrated UXCam and sent some sessions, great job! 🎉 But there's so much more you can do. Now, let's go further into setting things up.

<Cards columns={4}>
  <Card title="Tag Screens" href="https://developer.uxcam.com/docs/screen-tagging#/" icon="fa-mobile">
    Take full advantage of heatmaps and screen analytics.

    > Review Automatic or Manual Tagging
  </Card>

  <Card title="Mask PII Data" href="https://developer.uxcam.com/docs/sensitive-data-occlusion#/" icon="fa-credit-card">
    Protect your users’ privacy by masking sensitive data.

    > Mask or Blur Screens, Views and Fields
  </Card>

  <Card title="Assign User IDs" href="https://developer.uxcam.com/docs/users-and-properties#/" icon="fa-user">
    Get the full picture with user-level analytics.

    > Assign Custom User IDs and Properties
  </Card>

  <Card title="Send Events" href="https://developer.uxcam.com/docs/sending-events#/" icon="fa-question">
    Gain deeper insights into your users' interactions.

    > Send Events and Add Properties
  </Card>
</Cards>