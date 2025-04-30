---
title: Cordova
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
# UXCam Cordova Integration Guide

Welcome! This guide will help you set up UXCam for your Cordova project, enabling you to gather insights that are crucial for improving user experience. We'll guide you through the installation process, initial setup, and a few useful configurations to get the most out of UXCam.

## What Does a Successful Integration Look Like?

With a complete UXCam integration, you’ll be able to track user journeys, analyze user behaviors, and watch session replays, gaining valuable insights into how your users are interacting with your application. Following this quick guide, you’ll have your Cordova app connected to UXCam in no time.

### Minimum Requirements

Before starting the integration, ensure your project meets the following requirements:

* **AndroidX Enabled**: UXCam’s Cordova SDK requires AndroidX. Add the following to your `config.xml` file:

  ```xml
  <preference name="AndroidXEnabled" value="true" />
  ```

## Quick Start Guide

Integrating UXCam into your Cordova project only requires a few steps. With just a few lines of code, you'll be ready to capture user sessions and start analysing user behavior.

### Step 1: Install the UXCam Plugin

Add UXCam to your Cordova project by running the following command:

```coffeescript Cordova
cordova plugin add cordova-uxcam
```

### Step 2: Initialize UXCam

To initialize UXCam, you need to call its configuration method once the device is ready. You can typically do this in the `onDeviceReady()` function. Replace `'YOUR APP KEY'` with the actual key from your UXCam dashboard.

```coffeescript Cordova

    UXCam.optIntoSchematicRecordings(); // Enable session video recording on iOS

    const configuration = {
        userAppKey: 'YOUR APP KEY',
        enableAutomaticScreenNameTagging: false,
        enableImprovedScreenCapture: true,
    };
    
    UXCam.startWithConfiguration(configuration);
```

## Integration for Different Frameworks in Cordova:

### Ionic Integration

If you're using Ionic, here are the steps to integrate UXCam:

1. **Install UXCam for Ionic**

   ```coffeescript Ionic
   ionic cordova plugin add cordova-uxcam
   ```

2. **Add UXCam Declaration**

      In your `AppComponent.ts`, declare UXCam:

   ```typescript
   declare var UXCam: any;
   ```

3. **Initialize UXCam**

   Similar to Cordova, call the initialization method within the `platform.ready()` lifecycle hook:

   ```typescript
   import { Platform } from '@ionic/angular';
   ...
   constructor(private platform: Platform) {
       this.platform.ready().then(() => {
           UXCam.optIntoSchematicRecordings(); // Enable session video recording on iOS

           const configuration = {
               userAppKey: 'YOUR APP KEY',
               enableAutomaticScreenNameTagging: false,
               enableImprovedScreenCapture: true,
           };

           UXCam.startWithConfiguration(configuration);
       });
   }
   ```

### Capacitor Integration

To integrate UXCam with Capacitor:

1. **Install the UXCam Plugin**

   ```bash
   npm install cordova-uxcam
   ```

2. **Add UXCam Declaration**

      In your `AppComponent.ts` file, add:

   ```typescript
   declare var UXCam: any;
   ```

3. **Initialize UXCam**

   Call the configuration method after the `platform.ready()` hook:

   ```typescript
   import { Platform } from '@ionic/angular';
   ...
   constructor(private platform: Platform) {
       this.platform.ready().then(() => {
           UXCam.optIntoSchematicRecordings(); // Enable session video recording on iOS

           const configuration = {
               userAppKey: 'YOUR APP KEY',
               enableAutomaticScreenNameTagging: false,
               enableImprovedScreenCapture: true,
           };

           UXCam.startWithConfiguration(configuration);
       });
   }
   ```

## Troubleshooting Common Issues

### OkHttp Error

If you encounter the following error: *Failed resolution of: Lokhttp3/Callback;* follow these steps:

1. Navigate to `Platforms > Android > App > build.gradle`.
2. Add the following dependency:

   ```gradle
   implementation("com.squareup.okhttp3:okhttp:4.9.3") // or newer version
   ```

This should resolve the error, and your integration should complete successfully.

## Next Steps ➞

Now that you have UXCam integrated, your sessions will start appearing on the dashboard within a few seconds after the app goes into the background. We encourage you to explore customization options such as identifying users, tagging sessions, or hiding sensitive views. 

Check out our additional documentation to learn how to further tailor UXCam to your needs!

> 👍 **As Simple As That!**
>
> Your integration process is complete. Start reviewing sessions in the dashboard and explore UXCam's powerful features to get more insights into your app's user experience.

> 📜 **SDK Updates**
>
> To stay up to date with the latest SDK improvements, visit [this page](https://help.uxcam.com/hc/en-us/articles/4404509626509--SDK-UPDATES).
