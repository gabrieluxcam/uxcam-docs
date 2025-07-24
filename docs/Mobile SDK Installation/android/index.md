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
<GitHubCallout type="note">Need an account? \*\*[Start free](/signup)      \*\* – most teams complete this setup in **under 15 minutes**.</GitHubCallout>

<JumpCallout to="#android-sdk--quickstart">Jump to Quickstart</JumpCallout>

# Integration Journey at a Glance

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

<GitHubCallout type="tip"> ★ **Optional but highly recommended.** Ship steps 1-3 to start getting session replays and heatmaps, then add steps 4-5 for deeper analytics.</GitHubCallout>

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

<Image align="center" src="https://files.readme.io/5879b95c4dd8fb37220bdafe37a126498ca203c86da7fecbffb2dcd2fb241b47-image.png" />

<br />

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
* `.occlusions(Arrays.asList(occlusion))` For passing occlusion for screens from the SDK startup, we'll get to that [here](sensitive-data-occlusion/).
* `.enableCrashHandling(bool)` True by Default. Set to false if you want to disable crash capture.
* `.enableMultiSessionRecord(bool)` True by Default. Set to false to stop any session after the first one from being recorded.

***

## Video Tutorial

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=BZxD0snh9mE" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252FBZxD0snh9mE%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253DBZxD0snh9mE%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252FBZxD0snh9mE%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=BZxD0snh9mE" providerUrl="https://www.youtube.com/" providerName="YouTube" />

# Next Steps

You've successfully integrated UXCam and sent some sessions, great job! But there's so much more you can do. Now, let's go further into setting things up.

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