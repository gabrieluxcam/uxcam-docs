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

<GitHubCallout type="note">Need an account? \*\*[Start free](/signup)     \*\* – most teams finish this setup in **\< 15 min**.</GitHubCallout>

## Step 1: Add the SDK

In your module's **build.gradle**, add:

```groovy build.gradle (app)
repositories {
  maven { url 'https://sdk.uxcam.com/android/' }
}
dependencies {
  implementation 'com.uxcam:uxcam:3.+'
}
```

<br />

## 2 · Start Recording

<Tabs>
  <Tab title="Kotlin">
    ```groovy build.gradle (app)
    repositories {
      maven { url 'https://sdk.uxcam.com/android/' }
    }
    dependencies {
      implementation 'com.uxcam:uxcam:3.+'
    }
    ```
  </Tab>

  <Tab title="Java">
    ```groovy build.gradle (app)
    repositories {
    maven { url 'https://sdk.uxcam.com/android/' }
    }
    dependencies {
    implementation 'com.uxcam:uxcam:3.+'
    }
    ```
  </Tab>
</Tabs>