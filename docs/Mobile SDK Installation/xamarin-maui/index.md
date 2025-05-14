---
title: Xamarin - MAUI
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
# UXCam Xamarin / MAUI Integration Guide

Welcome! This guide will help you set up UXCam for your Xamarin project, enabling you to gather insights that are crucial for improving user experience. We'll guide you through the installation process, initial setup, and a few useful configurations to get the most out of UXCam.

## What Does a Successful Integration Look Like?

With a complete UXCam integration, you'll be able to track user journeys, analyze user behaviors, and watch session replays, gaining valuable insights into how your users are interacting with your application. Following this quick guide, you'll have your Xamarin app connected to UXCam in no time.

<br />

## Quick Start Guide

[![NuGet version](https://badge.fury.io/nu/UXCam.svg)](https://badge.fury.io/nu/UXCam/3.5.1)

Please download the latest Xamarin UXCam component from here\
For reference on including a component into your project visit here.

> 📘 **Note**
>
> .NET MAUI SDK
>
> If you're implementing the SDK using .NET MAUI instead of regular Xamarin, you'll have to download the package from [this link](https://www.nuget.org/packages/UXCamDotNet/1.0.0-beta) instead, all other implementation steps are identical as seen below.

<br />

## SDK Installation - MAUI

1. Import the UXCam agent header at the top of your MauiProgram.cs

```coffeescript Code
using Com.UXCam;
```

2. Add this call in your `CreateMauiApp` method in MauiProgram.cs

```coffeescript Code
UXCam.OptIntoSchematicRecordings();
var configuration = new UXCamConfiguration("YOUR_APP_KEY");

UXCam.Start(configuration);
```

<GitHubCallout type="note">Done! Your MAUI app should now have the SDK installed and ready to record some sessions. Please check our following pages for information on screens, properties, and privacy.</GitHubCallout>

## SDK Installation - Xmarin Deprecated Verdsion

### iOS

1. Import the UXCam agent header at the top of your AppDelegate.cs
   ```objectivec
   using Com.UXCam;
   ```

2. Add this call as the first line of your AppDelegate FinishedLaunching method
   ```objectivec
   UXCam.optIntoSchematicRecordings();

   UXCamConfiguration configuration = new UXCamConfiguration(
        userAppKey: 'userAppKey',
        enableImprovedScreenCapture: true,
   );
   UXCam.startWithConfiguration(configuration);
   ```

### Android

1. Edit AndroidManifest.xml to make sure the following permissions are present
   ```swift
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
   ```

2. Edit AndroidManifest.xml to Add following services inside your application tag
   ```swift
   <service android:name="com.uxcam.service.HttpPostService"/>
   ```

3. In every activity that is an entry point to your app add
   ```swift
   using Com.UXCam;
   ```

4. Add UXCam.startApplicationWithConfiguration("App-key") inside onCreate method
   ```coffeescript
   using Com.UXCam;
   using Com.Uxcam.Datamodel;
   UXCam.OptIntoVideoRecording();
   UXConfig config = new UXConfig.Builder("your app Key")
   .Build();
   UXCam.StartWithConfiguration(config);
   ```
   <br />

## Next Steps ➞

Now that you have UXCam integrated, your sessions will start appearing on the dashboard within a few seconds after the app goes into the background. We encourage you to explore customization options such as identifying users, tagging sessions, or hiding sensitive views.\
Check out our additional documentation to learn how to further tailor UXCam to your needs!

> 👍 **As Simple As That!**
>
> Your integration process is complete. Start reviewing sessions in the dashboard and explore UXCam's powerful features to get more insights into your app's user experience.
>
> 📜 **SDK Updates**
>
> To stay up to date with the latest SDK improvements, visit [this link](https://help.uxcam.com/hc/en-us/articles/4404509626509--SDK-UPDATES).