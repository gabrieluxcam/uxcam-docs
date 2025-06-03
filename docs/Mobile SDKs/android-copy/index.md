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
# Android SDK · Quickstart

<Callout type="info">
Need an account? **[Start free](/signup)** – most teams finish this setup in **< 15 min**.
</Callout>

## 1 · Add the SDK

```groovy title="build.gradle (app)"
repositories {
  maven { url 'https://sdk.uxcam.com/android/' }
}
dependencies {
  implementation 'com.uxcam:uxcam:3.+'
}
```

## 2 · Start Recording

<Tabs
  labels={['Kotlin','Java']}
  values={[
`import com.uxcam.UXCam
import com.uxcam.datamodel.UXConfig

class MyApp : Application() {
  override fun onCreate() {
    super.onCreate()
    val config = UXConfig.Builder(BuildConfig.UXCAM_KEY).build()
    UXCam.startWithConfiguration(config)
}`,
`import com.uxcam.UXCam;
import com.uxcam.datamodel.UXConfig;

public class MyApp extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    UXConfig config = new UXConfig.Builder(BuildConfig.UXCAM_KEY).build();
    UXCam.startWithConfiguration(config);
  }
}`]}
/>

<Callout type="success">
Watch Logcat for **“Verification successful”** – your first session should appear on the UXCam dashboard within ~2 minutes.
</Callout>

## 3 · Verify


---

## What’s Next?

<Table
  columns='["#", "Task", "Unlocks"]'
  rows='[
    ["★ 1", "Mask PII & Sensitive Content", "GDPR / CCPA compliance"],
    ["★ 2", "Tag Screens", "Heat‑maps & screen analytics"],
    ["★ 3", "Set User Identity & Properties", "Unified users, funnels & cohorts"],
    ["★ 4", "Log Custom Events", "Metric dashboards & alerts"]
  ]'
/>

<Callout type="note">
★ **Highly recommended:** ship this quickstart first, then add tasks 1‑4 when you’re ready for deeper insight.
</Callout>

### Resources
* **Mask PII guide** – overlay patterns for Views, Compose & WebView  
* **Screen Tagging guide** – automatic vs. manual tagging  
* **Events & User Identity guide** – code samples & best practice  
* **Troubleshooting** – crashes, ANRs, FAQs
