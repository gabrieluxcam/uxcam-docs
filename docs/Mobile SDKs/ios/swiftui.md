---
title: SwiftUI UXCam package
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
> 📘 **Note**
>
> All UXCam properties and methods are also available in UXCamSwiftUI without additional import of UXCam and can be accessed using `UXCamCore`. For eg. UXCam.isRecording() can be accessed into UXCamSwiftUI as UXCamCoreSwiftUI.isRecording()

[![pod version](https://img.shields.io/cocoapods/v/UXCamSwiftUI)](https://github.com/uxcam/ios-swiftui/tree/1.0.1)

# SDK Integration

If you're building your app using SwiftUI, please integrate UXCam using Swift Package Manager or Cocoapods using the following steps:

## Cocoapods

* Add to the Podfile

```swift
pod 'UXCamSwiftUI'
```

* Run `pod install` in the project directory to download dependency.

## Swift Package Manager

### Add UXCamSwiftUI Package:

<Image align="center" width="80%" src="https://files.readme.io/25df998-Screenshot_25_3_24__09_25.jpg" />

[https://github.com/uxcam/uxcam-ios-swiftui](https://github.com/uxcam/uxcam-ios-swiftui)

## Securely load your App Key

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Where
      </th>

      <th>
        How-To
      </th>

      <th>
        Pros / Cons
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        Info.plist placeholder (recommended)
      </td>

      <td>
        Add UXCamAppKey = $(UXCAM\_APP\_KEY) to Info.plist and define UXCAM\_APP\_KEY as a User‑Defined build setting (or in an ignored .xcconfig).
      </td>

      <td>
        * Keeps key out of source control
        * Simple to read at runtime
      </td>
    </tr>

    <tr>
      <td>
        CI env var injection
      </td>

      <td>
        Add `UXCAM_APP_KEY `to your CI secret store and pass -`DUXCAM_APP_KEY=$(UXCAM_APP_KEY)` to xcodebuild.
      </td>

      <td>
        * Zero key on developer laptops
        * Extra CI config
      </td>
    </tr>

    <tr>
      <td>
        Runtime fetch (remote config)
      </td>

      <td>
        Download the key from your config service before initialising UXCam.
      </td>

      <td>
        * Central rotation
        * Potential slight delay before first session
      </td>
    </tr>
  </tbody>
</Table>

```swift
// Bundle-based lookup (works for techniques 1 & 2)\
let appKey = Bundle.main.object(forInfoDictionaryKey: "UXCamAppKey") as! String
```

<br />

## Initialization Steps:

* Import UXCam at the top of your main App struct:

```swift
import UXCamSwiftUI
```

* Add an init method to your main App struct and add the following calls:

```swift
@main
struct MyApp: App {
    init() {
        // 1. Opt‑in for wireframe screenshots (App Store review safe)
        UXCamCore.optIntoSchematicRecordings()

        // 2. Build configuration
        var config = Configuration(appKey: appKey)
        config.enableAutomaticScreenNameTagging = true // default

        #if DEBUG
        // 🔍 Verbose integration logs only in Debug builds
        config.enableIntegrationLogging = true
        #endif

        // 3. Start UXCam
        UXCamSwiftUI.start(with: config)
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

&#x20;

| Where                           | When to Use                                                   | Code Snippet                                                                              |
| :------------------------------ | :------------------------------------------------------------ | :---------------------------------------------------------------------------------------- |
| SceneDelegate in mixed projects | You migrated screen‑by‑screen and still have a SceneDelegate. | Call the same init block in` scene(_:willConnectTo:options:).`                            |
| WidgetKit extension             | Record interactions inside a widget.                          | Import UXCamSwiftUI in the widget’s @main and call UXCamSwiftUI.start in init().          |
| Preview mode                    | Avoid SDK traffic in SwiftUI previews.                        | Guard with `if ProcessInfo.processInfo.environment["XCODE_RUNNING_FOR_PREVIEWS"] != "1".` |

<br />

> 👍
>
> That completes the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
> You can optionally use the API's for customizations such as identifying users from your database with UXCam, tagging sessions or hiding sensitive views.

> 🚧 **Warning**
>
> **Screen Tagging** and **Hiding Sensitive Data** for Swift UI have some differences and you can check some examples on the documentation, all other customization APIs provided by UXCam can be called regularly done with iOS by importing UXCam and calling the desired method.