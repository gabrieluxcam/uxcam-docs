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
<GitHubCallout type="note">Need an account? \*\*[Start free](/signup)   \*\* – most teams finish this setup in **\< 15 min**.</GitHubCallout>

<JumpCallout to="#android-sdk--quickstart">Jump to Quickstart</JumpCallout>

# Integration Journey at a Glance

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        #
      </th>

      <th>
        Step
      </th>

      <th>
        Goal
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        <strong>1</strong>
      </td>

      <td>
        <strong>Customise SDK Configuration</strong>  <a href="#1-initialise-sdk--start-recording"> Jump →</a>
      </td>

      <td>
        Capture your first live session
      </td>
    </tr>

    <tr>
      <td>
        <strong>3</strong>
      </td>

      <td>
        <strong>Tag Screens</strong>  <a href="#3-tag-screens"> Jump →</a>
      </td>

      <td>
        Enable heat-maps & screen analytics
      </td>
    </tr>

    <tr>
      <td>
        <strong>2</strong>
      </td>

      <td>
        <strong>Mask PII & Sensitive Content</strong>  <a href="#2-mask-pii--sensitive-content"> Jump →</a>
      </td>

      <td>
        Hide passwords and other GDPR/CCPA data
      </td>
    </tr>

    <tr>
      <td>
        <strong>4 ★</strong>
      </td>

      <td>
        <strong>Set User Identity & Properties</strong>  <a href="#4-set-user-identity--properties"> Jump →</a>
      </td>

      <td>
        Unify sessions, power funnels & cohorts
      </td>
    </tr>

    <tr>
      <td>
        <strong>5 ★</strong>
      </td>

      <td>
        <strong>Tag Events</strong>  <a href="#5-tag-events"> Jump →</a>
      </td>

      <td>
        Measure key actions & run final QA
      </td>
    </tr>
  </tbody>
</Table>

<GitHubCallout type="tip"> ★ **Optional but highly recommended.** Ship steps 1-3 to start getting replays and heat-maps, then add steps 4-5 for deeper analytics.</GitHubCallout>

<Accordion title="Key Benefits After Setup" icon="fa-info-circle">
  * **Session Replay + Heat-maps** – watch every UX moment in context.
  * **Advanced Product Analytics** – funnels, retention, feature adoption.
  * **Insight Alerts** – journey summaries, rage-tap & anomaly detection.
  * **Engineering Analytics** – client-side performance tied to real sessions, monitoring crashes, ANRs and handled exceptions with session replay & developer logs for faster debugging.
</Accordion>

<Accordion title="Tips Before You Begin" icon="fa-bolt">
  * Create **separate keys** for *debug* & *production* to keep data clean.
  * Use **feature flags** to toggle UXCam in staging builds.
  * Check Logcat for **“Verification successful”** and **“Session/Video uploaded”** messages to confirm everything is wired up.
</Accordion>

***

# Android SDK · Quickstart

## Step 1: Find your App Key 🔑

### 1.1: Grab your key

If you don't already have it, grab it in App Settings -> App Key on your <a href="https://app.uxcam.com" target="_blank" rel="noopener">UXCam Dashboard</a>.

<GitHubCallout type="important">**Create separate keys for debug/production.** Keep your data clean, no need to mix test actions with real user insights!</GitHubCallout>

### 1.2: Keep it out of source control:

add it to `local.properties` (already in the default `.gitignore`) or inject it via your CI, then expose it safely at compile-time so you never hard-code secrets

***

## Step 2: Add the SDK

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

<GitHubCallout type="important">Make sure to start the SDK at the earliest possible Context.</GitHubCallout>

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

<GitHubCallout type="success">This completes the most basic integration process.                          Watch Logcat for **“Verification successful”** – your first session should appear on the UXCam dashboard within \~2 minutes.</GitHubCallout>

***

# Advanced SDK Configuration

You can control how the SDK is configured via the configuration object you create, the available parameters are below:

## Want to debug?

Use our configuration option `.enableIntegrationLogging(bool)` to view more verbose logs in your Logcat, this will help you verify everything and pinpoint possible hurdles you face down the line.

## Rest of Options

* `.enableAutomaticScreenNameTagging(bool)`  True by Default. Set to false if you want to disable automatic screen tagging.
* `.occlusions(Arrays.asList(occlusion))` For passing occlusion for screens from the SDK startup, we'll get to that [here]().
* `.enableCrashHandling(bool)` True by Default. Set to false if you want to disable crash capture.
* `.enableMultiSessionRecord(bool)` True by Default. Set to false to stop any session after the first one from being recorded.

***

# Next Steps

<Cards columns={4}>
  <Card title="Tag Screens" href="https://readme.com" icon="fa-home" target="_blank">
    Take Full Advantage of Heatmaps and Screen Analytics

    > Review Automatic or Manual Tagging
  </Card>

  <Card title="Mask PII Data" icon="fa-user">
    Protect Your Users’ Privacy and PII Data

    > Mask or Blur Screens, Views and Fields
  </Card>

  <Card title="Assign User IDs" icon="fa-star">
    Get The Full Picture with User Analytics

    > Assign Custom User IDs and Properties
  </Card>

  <Card title="Send Events" icon="fa-question">
    Deeper Insights of Your Users' interactions

    > Send Events and Add Properties
  </Card>
</Cards>