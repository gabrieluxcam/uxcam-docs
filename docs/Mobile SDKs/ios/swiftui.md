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

## Initialization Steps:

* Import UXCam at the top of your main App struct:

```swift
import UXCamSwiftUI
```

* Add an init method to your main App struct and add the following calls:

```swift
init(){
     UXCamCore.optIntoSchematicRecordings()
     let config = Configuration(appKey: "YOUR APP KEY")
     config.enableAutomaticScreenNameTagging = true
     UXCamSwiftUI.start(with: config)
}
```

> 👍
>
> That completes the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
> You can optionally use the API's for customizations such as identifying users from your database with UXCam, tagging sessions or hiding sensitive views.

> 🚧 **Warning**
>
> **Screen Tagging** and **Hiding Sensitive Data** for Swift UI have some differences and you can check some examples on the documentation, all other customization APIs provided by UXCam can be called regularly done with iOS by importing UXCam and calling the desired method.