---
title: Quick Start - iOS
excerpt: Get UXCam running in your iOS app in 5 minutes
deprecated: false
hidden: false
metadata:
  title: 'iOS Quick Start - UXCam'
  description: 'Fastest way to add UXCam session recording to your iOS app'
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: ios
      title: Full iOS Guide
---

# iOS Quick Start

Get session recording working in your iOS app in under 5 minutes.

## Prerequisites

- Xcode
- iOS deployment target 12.0+
- A [UXCam account](https://app.uxcam.com/signup) with an app key

---

## Step 1: Add the Dependency

### CocoaPods

Add to your `Podfile`:

```ruby
pod 'UXCam'
```

Then run:

```bash
pod install
```

### Swift Package Manager

Add this URL in Xcode: `https://github.com/uxcam/uxcam-ios-sdk`

---

## Step 2: Initialize UXCam

In your `AppDelegate`:

```swift
import UXCam

func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")
    UXCam.optIntoSchematicRecordings()
    UXCam.start(with: config)

    return true
}
```

<Accordion title="Objective-C version">
```objectivec
#import <UXCam/UXCam.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    UXCamConfiguration *config = [[UXCamConfiguration alloc] initWithAppKey:@"YOUR_APP_KEY"];
    [UXCam optIntoSchematicRecordings];
    [UXCam startWithConfiguration:config];

    return YES;
}
```
</Accordion>

<GitHubCallout type="note">**SwiftUI?** Check our dedicated [SwiftUI integration guide](/docs/swiftui-ios) for SwiftUI-specific setup.</GitHubCallout>

---

## Step 3: Verify It Works

1. Run your app on a simulator or device
2. Navigate through a few screens
3. Send the app to background
4. Check your [UXCam Dashboard](https://app.uxcam.com) - your session should appear within 30 seconds

<GitHubCallout type="tip">Set `config.enableIntegrationLogging = true` to see UXCam debug logs in Xcode console.</GitHubCallout>

---

## Next Steps

You're recording sessions! Now customize your integration:

<Cards columns={2}>
  <Card title="Full iOS Guide" href="/docs/ios" icon="fa-brands fa-apple">
    Complete setup with all configuration options
  </Card>

  <Card title="Tag Screens" href="/docs/screen-tagging-ios" icon="fa-solid fa-mobile">
    Enable heatmaps and screen analytics
  </Card>

  <Card title="Mask Sensitive Data" href="/docs/sensitive-data-occlusion-ios" icon="fa-solid fa-eye-slash">
    Protect passwords and PII
  </Card>

  <Card title="Track Events" href="/docs/ios/events" icon="fa-solid fa-bolt">
    Capture custom user actions
  </Card>
</Cards>
