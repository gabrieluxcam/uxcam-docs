---
title: iOS SDK Integration Guide
excerpt: How to Get Started with UXCam for iOS
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
<GitHubCallout type="note">Need an account? \*\*[Start free](/signup)   \*\* – most teams complete this setup in **under 15 minutes**..</GitHubCallout>

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

<br />

> 🚧 Note for SwiftUI developers:
>
> If you're working with SwiftUI, you may add the dedicated SwiftUI SDK instead of the regular iOS one, you can check the initial setup [here](https://developer.uxcam.com/v1.1/docs/swiftui).

# What Does a Successful Integration Look Like?

With a solid integration, you’ll have a complete picture of how users interact with your app. From screen journeys and user behaviors to session replays and user properties, you’ll be able to understand and enhance every aspect of your product’s user experience. Follow along with this quick guide, and you’ll be up and running in no time.

## Quick Start: Only a Couple of Lines of Code

<GitHubReleaseBadge owner="uxcam" repo="uxcam-ios-sdk" />

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing user sessions in your test app.

### Adding the Dependencies

#### Cocoapods

* Add UXCam to your `Podfile`:

`pod 'UXCam'`

* Run <code>pod install</code> in the project directory to download dependency.

<Terminal>
  {`
          $ pod install
        `}
</Terminal>

#### Swift Package Manager

* If you prefer Swift Package Manager, you can add UXCam using the following URL:

`https://github.com/uxcam/uxcam-ios-sdk`

Specify the exact version to ensure compatibility.

## Initializing UXCam

1. Imports:

```coffeescript Swift
import UXCam
```

2. Configuration

Add the following initialisation to `application:didFinishLaunchingWithOptions:` in your `AppDelegate`:

```swift
let configuration = UXCamConfiguration(appKey: "YourAppKey")
```

> Replace "YourAppKey" with the app key available on your UXCam dashboard.

3. Then, start the SDK with your configuration object

```swift
UXCam.optIntoSchematicRecordings()
UXCam.start(with: configuration)
```

***

## Available Configuration Options:

<p style={{ fontSize: "18px" }}>
  <code class="language-java">enableAutomaticScreenNameTagging</code>

  <br />

  <em style={{ fontSize: "13px" }}>Used to disable automatic screen tagging if manual tagging of the screens will be done, default is true</em>
</p>

<p style={{ fontSize: "18px" }}>
  <code class="language-java">enableAdvancedGestureRecognition</code>

  <br />

  <em style={{ fontSize: "13px" }}>Control the gesture recognizers used by UXCam. Set TRUE to enable the full range of gesture recognizers (the default) or FALSE to limit it to basic gestures.</em>
</p>

<p style={{ fontSize: "18px" }}>
  <code class="language-java">enableCrashHandling:</code>

  <br />

  <em style={{ fontSize: "13px" }}>Crash handling is enabled by default, to let you capture all the sessions with crashes and see the corresponding crash logs.</em>
</p>

> 👍 As Simple As That!
>
> This will complete the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
>
> We recommend that after you've set this up and have reviewed some sessions from your tests, get to the customisation features UXCam offers, let's go to the next steps!

<br />

> 📘 Additional Resources
>
> **[SDK Updates](https://developer.uxcam.com/docs/changelog):** Stay up to date with the latest features and improvements.
>
> **[Tailor for Success](https://help.uxcam.com/hc/en-us/articles/360022226651-How-to-customize-the-UXCam-account):** Learn how to use advanced features such as identifying users, tagging sessions, or hiding sensitive views.

<br />

## Next Steps ➡️

You’ve successfully integrated UXCam and sent some sessions, great job! 🎉  But there's so much more you can do. Now, let’s go further into setting things up.