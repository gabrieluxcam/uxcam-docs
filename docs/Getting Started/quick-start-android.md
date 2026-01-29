---
title: Quick Start - Android
excerpt: Get UXCam running in your Android app in 5 minutes
deprecated: false
hidden: false
metadata:
  title: 'Android Quick Start - UXCam'
  description: 'Fastest way to add UXCam session recording to your Android app'
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: android
      title: Full Android Guide
---

# Android Quick Start

Get session recording working in your Android app in under 5 minutes.

## Prerequisites

- Android Studio
- minSdkVersion 21+
- A [UXCam account](https://app.uxcam.com/signup) with an app key

---

## Step 1: Add the Dependency

In your module's `build.gradle`:

```groovy
repositories {
  maven { url 'https://sdk.uxcam.com/android/' }
}
dependencies {
  implementation 'com.uxcam:uxcam:3.+'
}
```

---

## Step 2: Initialize UXCam

In your Application class:

```kotlin
import com.uxcam.UXCam
import com.uxcam.datamodel.UXConfig

class MyApp : Application() {
  override fun onCreate() {
    super.onCreate()
    val config = UXConfig.Builder("YOUR_APP_KEY").build()
    UXCam.startWithConfiguration(config)
  }
}
```

<Accordion title="Java version">
```java
import com.uxcam.UXCam;
import com.uxcam.datamodel.UXConfig;

public class MyApp extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    UXConfig config = new UXConfig.Builder("YOUR_APP_KEY").build();
    UXCam.startWithConfiguration(config);
  }
}
```
</Accordion>

---

## Step 3: Verify It Works

1. Run your app on a device or emulator
2. Navigate through a few screens
3. Send the app to background
4. Check your [UXCam Dashboard](https://app.uxcam.com) - your session should appear within 30 seconds

<GitHubCallout type="tip">Enable debug logs with `.enableIntegrationLogging(true)` on your config to see UXCam messages in Logcat.</GitHubCallout>

---

## Next Steps

You're recording sessions! Now customize your integration:

<Cards columns={2}>
  <Card title="Full Android Guide" href="/docs/android" icon="fa-brands fa-android">
    Complete setup with all configuration options
  </Card>

  <Card title="Tag Screens" href="/docs/screen-tagging" icon="fa-solid fa-mobile">
    Enable heatmaps and screen analytics
  </Card>

  <Card title="Mask Sensitive Data" href="/docs/sensitive-data-occlusion" icon="fa-solid fa-eye-slash">
    Protect passwords and PII
  </Card>

  <Card title="Track Events" href="/docs/android/events" icon="fa-solid fa-bolt">
    Capture custom user actions
  </Card>
</Cards>
