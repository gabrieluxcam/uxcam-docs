---
title: Quick Start - Cordova
excerpt: Get UXCam running in your Cordova app in 5 minutes
deprecated: false
hidden: false
metadata:
  title: 'Cordova Quick Start - UXCam'
  description: 'Fastest way to add UXCam session recording to your Cordova app'
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: cordova
      title: Full Cordova Guide
---

# Cordova Quick Start

Get session recording working in your Cordova app in under 5 minutes.

## Prerequisites

- Apache Cordova CLI
- iOS deployment target 12.0+ / Android minSdkVersion 21+
- A [UXCam account](https://app.uxcam.com/signup) with an app key

---

## Step 1: Add the Plugin

```bash
cordova plugin add cordova-uxcam
```

---

## Step 2: Initialize UXCam

Add this to your `deviceready` handler:

```javascript
document.addEventListener('deviceready', function() {

  UXCam.optIntoVideoRecordings();

  UXCam.startWithConfiguration({
    userAppKey: 'YOUR_APP_KEY'
  });

}, false);
```

---

## Step 3: Verify It Works

1. Build and run your app
2. Navigate through a few screens
3. Send the app to background
4. Check your [UXCam Dashboard](https://app.uxcam.com) - your session should appear within 30 seconds

---

## Next Steps

You're recording sessions! Now customize your integration:

<Cards columns={2}>
  <Card title="Full Cordova Guide" href="/docs/cordova" icon="fa-solid fa-mobile-screen">
    Complete setup with all configuration options
  </Card>

  <Card title="Tag Screens" href="/docs/screen-tagging-cordova" icon="fa-solid fa-mobile">
    Enable heatmaps and screen analytics
  </Card>

  <Card title="Mask Sensitive Data" href="/docs/sensitive-data-occlusion-cordova" icon="fa-solid fa-eye-slash">
    Protect passwords and PII
  </Card>

  <Card title="Track Events" href="/docs/cordova/events" icon="fa-solid fa-bolt">
    Capture custom user actions
  </Card>
</Cards>
