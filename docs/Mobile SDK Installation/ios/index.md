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
<GitHubCallout type="note">Need an account? \*\*[Start free](/signup)                      \*\* – most teams complete this setup in **under 15 minutes**..</GitHubCallout>

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

> 🚧 Note for SwiftUI developers:
>
> If you're working with SwiftUI, you may add the dedicated SwiftUI SDK instead of the regular iOS one, you can check the initial setup [here](https://developer.uxcam.com/docs/swiftui#/).

<br />

## Step 1: Get your App Key

### 1.1: Find your app key

If you don't already have it, find it in App Settings -> App Key on your <a href="https://app.uxcam.com" target="_blank" rel="noopener">UXCam Dashboard</a>.

<Image align="center" src="https://files.readme.io/5879b95c4dd8fb37220bdafe37a126498ca203c86da7fecbffb2dcd2fb241b47-image.png" />

<GitHubCallout type="important">**Create separate keys for debug/production.** Keep your data clean, no need to mix test actions with real user insights!</GitHubCallout>

### 1.2: Keep it out of source control:

Put the key in an ignored `.xcconfig` file or pass it in as an environment variable from your CI, then reference it via a build-setting placeholder (e.g. `$(UXCAM_APP_KEY)`) so the value is injected at compile-time and never hard-coded in source.

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
let configuration = UXCamConfiguration(appKey: "YOUR_APP_KEY")
```

> Replace "YourAppKey" with the app key available on your UXCam dashboard.

3. Then, start the SDK with your configuration object

```swift
import UXCam

func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")

    UXCam.optIntoSchematicRecordings()

    #if DEBUG
    // 🔍 Enable verbose integration logs in Debug builds only
    config.enableIntegrationLogging = true
    #endif

    UXCam.start(with: config)
    return true
}
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

## Build & Test

Run the app on a simulator or device, navigate a few screens, then send it to the background. Your first session replay will appear on the UXCam dashboard within seconds.

> 👍 As Simple As That!
>
> This will complete the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
>
> We recommend that after you've set this up and have reviewed some sessions from your tests, get to the customisation features UXCam offers, let's go to the next steps!

## Video Tutorial:

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=i9ReMW6sL9k" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252Fi9ReMW6sL9k%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253Di9ReMW6sL9k%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252Fi9ReMW6sL9k%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=i9ReMW6sL9k" providerUrl="https://www.youtube.com/" providerName="YouTube" />

## Next Steps ➡️

You’ve successfully integrated UXCam and sent some sessions, great job! 🎉  But there's so much more you can do. Now, let’s go further into setting things up.

<Cards columns={4}>
  <Card title="Tag Screens" href="https://developer.uxcam.com/docs/screen-tagging-ios#/" icon="fa-mobile">
    Take full advantage of heatmaps and screen analytics.

    > Review Automatic or Manual Tagging
  </Card>

  <Card title="Mask PII Data" href="https://developer.uxcam.com/docs/sensitive-data-occlusion-ios#/" icon="fa-credit-card">
    Protect your users’ privacy by masking sensitive data.

    > Mask or Blur Screens, Views and Fields
  </Card>

  <Card title="Assign User IDs" href="https://developer.uxcam.com/docs/custom-users-and-properties-ios#/" icon="fa-user">
    Get the full picture with user-level analytics.

    > Assign Custom User IDs and Properties
  </Card>

  <Card title="Send Events" href="https://developer.uxcam.com/docs/custom-events-and-properties-ios#/" icon="fa-question">
    Gain deeper insights into your users' interactions.

    > Send Events and Add Properties
  </Card>
</Cards>