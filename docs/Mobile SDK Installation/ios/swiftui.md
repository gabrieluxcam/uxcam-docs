---
title: SwiftUI
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
> 📘 
> 
> All UXCam properties and methods are also available in UXCamSwiftUI without additional import of UXCam and can be accessed using `UXCamCore`. For eg. UXCam.isRecording() can be accessed into UXCamSwiftUI as UXCamCoreSwiftUI.isRecording()

[![pod version](https://img.shields.io/cocoapods/v/UXCamSwiftUI)](https://github.com/uxcam/ios-swiftui/tree/1.0.1)

# SDK Integration

If you're building your app using SwiftUI, please integrate UXCam using Swift Package Manager or Cocoapods using the following steps:

## Cocoapods

- Add to the Podfile

<pre><code class="language-swift">pod 'UXCamSwiftUI'</code></pre>

- Run <code>pod install</code> in the project directory to download dependency.

## Swift Package Manager

### Add UXCamSwiftUI Package:

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/25df998-Screenshot_25_3_24__09_25.jpg",
        "SwiftUI package.png",
        902
      ],
      "align": "center",
      "sizing": "80",
      "caption": "<https://github.com/uxcam/uxcam-ios-swiftui>"
    }
  ]
}
[/block]


## Initialization Steps:

- Import UXCam at the top of your main App struct:

<pre><code class="language-java">import UXCamSwiftUI
</code></pre>

- Add an init method to your main App struct and add the following calls:

<pre><code class="language-swift">init(){
     UXCamCore.optIntoSchematicRecordings()
     let config = Configuration(appKey: "YOUR APP KEY")
     config.enableAutomaticScreenNameTagging = true
     UXCamSwiftUI.start(with: config)
}</code></pre>

> 👍 
> 
> That completes the integration process.  
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.  
> You can optionally <a href="https://help.uxcam.com/hc/en-us/articles/360022226651-Tailor-For-Success">use the API's</a> for customizations such as identifying users from your database with UXCam, tagging sessions or hiding sensitive views.

> 🚧 
> 
> Automatic Screen Tagging is available only from SDK plugin v1.0.7
> 
> **Screen Tagging** and **Hiding Sensitive Data** for Swift UI have some differences and you can check some examples on the documentation, all other customization APIs provided by UXCam can be called regularly done with iOS by importing UXCam and calling the desired method.

> 📘 SDK Updates
> 
> To check all the information on the fixes and improvements on the latest versions of the SDK, please visit [this page](https://help.uxcam.com/hc/en-us/articles/4404509626509--SDK-UPDATES).