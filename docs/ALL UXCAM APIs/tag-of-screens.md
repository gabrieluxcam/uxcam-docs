---
title: Tag Screen Name
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
  pages:
    - type: basic
      slug: hide-sensitive-views
      title: Hide Sensitive Views
---
UXCam captures the Screen (view controller/activity) name **automatically**. However, there are some cases where you will need to manually tag your screens to ensure you have them separately in your recordings and heatmaps. 

> 📘 Important
> 
> For more information on how to automatically capture screens in Flutter and Fragments on Android please visit this respective links: 
> 
> - [Flutter automatic screen capture](https://developer.uxcam.com/docs/flutter-tagging-approach)
> - [Fragment based tagging](https://developer.uxcam.com/docs/fragment-based-tagging)

**Use our API to tag screens if you:**

- Use **SwiftUI, React Native or Cordova**, where the application usually runs within a single activity or controller.
- Want to define which views or activities should be considered screens or want to define different names.
- Use **OpenGL** or have a game where the screens are dynamically shown

## Tag Screen Name

> 👍 Recommendation
> 
> **iOS:**
> 
> We strongly recommend using only automatic screen name tagging, however if it is extremely necessary, after improvements made on iOS SDK version **3.5.1**, to manually tag screens along with automatic tagging at the same time, **UXCam.tagScreenName** should be called in **viewWillAppear**  method of your screens.
> 
> **Android:**
> 
> If you must manually tag activities, you should do so inside the `onCreate()` method of your activity, and if you're manually tagging some fragments, this should be done inside the `onResume()` method of the fragment.

```swift iOS
UXCam.tagScreenName(_ screenName: String)

//Example  
override open func viewWillAppear(_ animated: Bool) {
      super.viewWillAppear(animated)
      UXCam.tagScreenName("Home Screen Manual")
}
```
```java Android
UXCam.tagScreenName(String screenName);

// Example Activity Tagging
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
      
      UXCam.tagScreenName("Screen Name A")
      // ... your code
      }


// Example Fragment Tagging
    override fun onResume() {
        super.onResume()
        UXCam.tagScreenName("Fragment Name B")
    }
```
```javascript React Native
RNUxcam.tagScreenName: (screenName: string) => void
```
```javascript Flutter
void tagScreenName(String screenName)
```
```swift SwiftUI
uxcamTagScreenName(_ screenName: String) -> some View

//eg. 
var body: some View
{
	Text("Hello world")
		.uxcamTagScreenName("HelloScreen")
}
```
```csharp Xamarin
void UXCam.TagScreenName(string screenName)
```
```javascript Cordova
UXCam.tagScreenName: (screenName: string) => void
```
```javascript NativeScript
NSUXCam.tagScreenName: (screenName: string) => void
```



> 📘 
> 
> The API parameters are:  
> **screenName**: A String with name for the screen as required.

## Control Automatic Tagging

> 📘 
> 
> **Note**: Cross-platform frameworks often have inconsistent view controller/activity naming; it is recommended to manually tag your screens if you're using such frameworks.

By default, automatic screen name tagging is set to true, so to **enable** or **disable** the automatic screen tagging you should do so from the configuration object at SDK startup:

```c iOS
let configuration = UXCamConfiguration(appKey: "YourAppKey")

//Example      
configuration.enableAutomaticScreenNameTagging = true // call it False in order to disable the Automatic Screen taggin
```
```coffeescript Android
UXConfig config = new UXConfig.Builder('yourAppKey')
    .enableAutomaticScreenNameTagging(true) // call it False in order to disable the Automatic Screen tagging
    .enableImprovedScreenCapture(true)
    .build()
```
```coffeescript Flutter
FlutterUxConfig config = FlutterUxConfig(
      userAppKey: "UXCAM_APP_KEY",
      enableAutomaticScreenNameTagging: false);
   
```
```coffeescript React Native
const configuration = {
    userAppKey: 'YOUR API KEY',
    enableAutomaticScreenNameTagging: false,
    enableImprovedScreenCapture: true
 }
```
```coffeescript Cordova
const configuration = {
    userAppKey: 'YOUR API KEY',
    enableAutomaticScreenNameTagging:false, // default is true
    enableImprovedScreenCapture:true,
}
```
```coffeescript Xamarin
UXCamConfiguration configuration = new UXCamConfiguration(
     userAppKey: ‘userAppKey’,
     enableImprovedScreenCapture: true,
     enableAutomaticScreenNameTagging:false, //default is true
);
```



## Scrolling Screens Examples:

> 📘 For screens that scroll:
> 
> - Tag screen name as usual - you can find an instruction above
>   - When a user scrolls to 800 points - change the screen name using our tag API. The scroll area is calculated by default UIKit methods - it should be one of the “scroll view delegate methods”
>   - The same follows for the third section 800\*2 = 1600. Once a user scrolls to 1600, change to the 3rd screen name.
>   - When users scroll up, the same calculation can be applied.
> 
> The biggest challenge that can appear is if users have both section 1 and some part of section 2 visible (it can usually happen on big iPad screens for apps not optimised for iPads). However, if it’s not the case for your app, you can go ahead and use this workaround for the long screens.