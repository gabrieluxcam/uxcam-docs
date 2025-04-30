---
title: Sensitive Data Occlusion In React Native Apps
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document outlines how to handle sensitive data in React Native
    applications using UXCam's features to ensure privacy compliance,
    emphasizing the occlusion of sensitive information like PII through text
    fields, views, and screens. It provides guidance on using UXCam's dashboard
    and SDK to apply occlusion rules, such as blurring or overlaying screens, to
    protect user data while maintaining the ability to record gestures if
    needed.
  robots: index
next:
  description: ''
---
In this section, we focus on how to handle sensitive data within your React Native application using UXCam’s features to ensure privacy compliance. It is essential to occlude sensitive information to protect user data like passwords, credit card numbers, or any other Personally Identifiable Information (PII).

UXCam ensures that as a controller you can use our platform and fulfil your obligations under GDPR. However, if you collect any **PII data** in your app such as email address, phone, or credit card number you should use our API to hide it.

You can choose to hide:

- **Text Fields**: Hide PII collected through text input fields
- **Views**: Hide specific views on the screen.
- **Screens: **Hide entire screens, such as payment pages

<br />

Sensitive information is hidden directly on the device by applying red boxes or blurring, meaning this data is never sent to UXCam servers. Ensure that all sensitive data is hidden before releasing your app to production.

> **Note**: When occluding sensitive data, you still have the option to record gestures for those views or screens. If you’re occluding passwords or other sensitive inputs, we recommend also disabling gesture recording.

***

## Are there any elements occluded right out of the box?

React Native provides the following occlusion behaviour by default:

- Any text input tagged with `uxcam-occlude` or `<input type="password">` is occluded.

***

# Add Occlusions Directly From Your Dashboard:

You can now add occlusion rules to your app directly from your dashboard. Simply go into your app's settings in your dashboard and enable the rules you need, below is a breakdown of how to take full advantage of this feature, without having to resort to adding additional code in your app for most scenarios:

### Occlude All Screens from Dashboard

From your app's settings in the UXCam dashboard, you'll see the **video recording privacy** section, from there, you'll see the first option to either record, occlude or blur all screens in your app.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/751b737-image.png",
        null,
        "Blur option will also enable you to select the blur radius (strength) once selected."
      ],
      "align": "center",
      "caption": "Blur option will also enable you to select the blur radius (strength) once selected."
    }
  ]
}
[/block]


### Screen Specific Occlusion Rules from Dashboard

You can also customise which screens you want to apply occlusion rules to, and can create multiple rules, for example, blurring a particular screen but occluding others:

![](https://files.readme.io/6b8810f-small-Staging_-_UXCam_Dashboard.png)

### Occlude Text Input Fields from Dashboard

You can also choose to occlude all text input fields on a specific or multiple screens by simply checking the option and selecting the screens you'd like to occlude the text inputs in. 

![](https://files.readme.io/253cbf0-small-Staging_-_UXCam_Dashboard.png)

> 📘 
> 
> Additionally, you can opt to record gestures on all blurred/occluded screens by toggling on the option 
> 
> ![](https://files.readme.io/33bf4ad-image.png)

### Occlusion priority:

- Screen specific overlay from Dashboard
- Screen specific blur from Dashboard
- Global blur/overlay from Dashboard that is applied to all screens
- Screen specific Overlay from SDK
- Screen specific Blur from SDK
- Global blur/overlay from SDK that is applied to all screens
- Global blur/overlay from SDK that has Record exception screens

### Limitations:

- Hiding sensitive **Views** needs to be handled from code (see [here](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view))

# Occlusion Setup from SDK Code

If you prefer to manually handle occlusions in your app or [occlude specific views](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view) instead of entire screens, read below for guidance on how to set it up. 

## Occlude The Entire Screen with Overlay

You can configure different overlay options with the following:

```coffeescript React Native
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

// IF USING SDK VERSION 6.0.0 PLEASE USE:
// import { OcclusionType } from 'react-native-ux-cam/src/types';

const overlay = {
   type: UXCamOcclusionType.Overlay, // compulsory to determine overlay type
	 color: 0xff00ee // hex integers in 0xrrggbb format
   hideGestures: true // optional, default true
   screens: [“screen1”, “screen2”] // optional, default all screens
 }

RNUxcam.applyOcclusion(overlay); // apply overlay
RNUxcam.removeOcclusion(overlay); // remove overlay
```

**Available overlay options are: **

<p style="font-size: 17px"><code class="language-java">withoutGesture(boolean withoutGesture) || hideGestures(boolean hideGestures)</code></br>
<em style="font-size: 12px">Allows the user to configure wether to capture gesture in the occluded screen or not. Passing in false to this method tells the SDK to capture gestures. Default is true, so by default the gestures are not captured.</em></p>

<p style="font-size: 17px"><code class="language-java">screens(List < String > screens)</code> - Use it in the configuration object</br>
<em style="font-size: 12px">Allows you to define the screens where the overlay is to either be applied or not, depending on the value passed to <strong>excludeMentionedScreens(boolean excludeMentionedScreens)</strong>.

By default, if no screens are passed, the overlay is applied to all the screens unless explicitly removed. This acts as a global setting and will override all other occlusion settings defined for all screens. The occlusion must be removed to revert this action.

If screens are passed, you have the ability to either apply overlay on the mentioned screens or to exclude the mentioned screens from being overlayed.</em></p>

<p style="font-size: 17px"><code class="language-java">excludeMentionedScreens(boolean excludeMentionedScreens)</code></br>
<em style="font-size: 12px">This option should be used in conjunction with <strong>screens(List < String > screens)</strong>.

 If the passed in value is true, it tells the SDK to exclude the mentioned screens from occlusion, while applying the occlusion to the rest of the screens in the app. 

If the passed in value is false, it tells the SDK to apply occlusion only to the screens that have been passed.

Default value is false.</em></p>

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/095be49-Overlay.png",
        "Overlay.png",
        2534
      ],
      "align": "center",
      "sizing": "80",
      "caption": "You'll see the desired screen completely hidden while your users navigate through it."
    }
  ]
}
[/block]


## Blur The Entire Screen

Blur is an occlusion API that allows you to blur screen records of screens. This lets you obtain information regarding the state of the screen and user interaction, while also maintaining privacy in sensitive screens.

This is useful to set all the occlusion/Blur from one place of the application without having to set it individually in different screens. 

You can configure different options using the following: 

```coffeescript React Native
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

// IF USING SDK VERSION 6.0.0 PLEASE USE:
// import { OcclusionType } from 'react-native-ux-cam/src/types';

const blur = {
   type: UXCamOcclusionType.Blur, // compulsory to determine blur type
   blurRadius: 20 // optional default 10
   hideGestures: true // optional, default true
   screens: [“screen1”, “screen2”] // optional, default all screens
 }
 
RNUxcam.applyOcclusion(blur); // apply blur
RNUxcam.removeOcclusion(blur); // remove blur
```

**Available blur options are: **

<p style="font-size: 17px"><code class="language-java">blurRadius(int blurRadius)</code></br>
<em style="font-size: 12px">This option allows you to define the blur radius to be used for blurring. The higher the value, the more blurred the resulting video is going to be. </em></p>

<p style="font-size: 17px"><code class="language-java">withoutGesture(boolean withoutGesture) || hideGestures(boolean hideGestures)</code></br>
<em style="font-size: 12px">Same as overlay. Please refer to overlay section.</em></p>

<p style="font-size: 17px"><code class="language-java">screens(List < String > screens)</code> - Use it in the configuration object</br>
<em style="font-size: 12px">Same as overlay. Please refer to overlay section.</em></p>

<p style="font-size: 17px"><code class="language-java">excludeMentionedScreens(boolean excludeMentionedScreens)</code></br>
<em style="font-size: 12px">Same as overlay. Please refer to overlay section.</em></p>

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/4b4c4ce-UXCam_Dashboard_-_24_May_2022_1_1.gif",
        "UXCam Dashboard - 24 May 2022 (1) (1).gif",
        736
      ],
      "align": "center",
      "caption": "You'll see your desired screens with a blur on top."
    }
  ]
}
[/block]


**Examples on blur radius property customization:**

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/72a8f8d-Blur_Results_Comparison_-_Product_Development_-_Confluence.png",
        null,
        "Blur radius customization examples"
      ],
      "align": "center",
      "caption": "Blur radius customization examples"
    }
  ]
}
[/block]


***

## Occlude all Text Fields

Similar to the new Overlay and Blur APIs:

```coffeescript React Native
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

// IF USING SDK VERSION 6.0.0 PLEASE USE:
// import { OcclusionType } from 'react-native-ux-cam/src/types';

const hideTextFields = {
   type: UXCamOcclusionType.OccludeAllTextFields
   screens: [“screen1”, “screen2”] // optional, default all screens
 }
 
RNUxcam.applyOcclusion(hideTextFields); // apply hide text fields
RNUxcam.removeOcclusion(hideTextFields); // remove hide text fields

// IMPORTANT: Please keep in mind that this API will only hide <TextInput>
// components. If you wish to hide <Text> components, refer to hiding Views.
```

> **NOTE: **Please keep in mind that this API will only hide `<TextInput>`  
> components. If you wish to hide `<Text>` components, refer to hiding Views below.

<br />

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/9caa54d-TextFields.png",
        "TextFields.png",
        2534
      ],
      "align": "center",
      "sizing": "80",
      "caption": "All fields identified as text will be occluded."
    }
  ]
}
[/block]


## Hide Sensitive View

Use it to hide specific views with sensitive information that you don't want to record.

The API parameters are:

**sensitiveView**: A View object that contains sensitive information.

```coffeescript React Native
RNUxcam.occludeSensitiveView: (sensitiveView: any) => void
    
//Example
<Button ref= {view => RNUxcam.occludeSensitiveView(view)}/>
```

## Usage from configuration object

It's also possible to pass a list of occlusions (except Sensitive View) to be applied during configuration.  For example:

```java React Native
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

// IF USING SDK VERSION 6.0.0 PLEASE USE:
// import { OcclusionType } from 'react-native-ux-cam/src/types';

const blur = {
    type: UXCamOcclusionType.Blur, // compulsory to determine blur type
    blurRadius: 20, // optional default 10
    hideGestures: true, // optional, default true
    screens: ["screen1", "screen2"] // optional, default all screens 
}
 
const overlay = {
   type: UXCamOcclusionType.Overlay, // compulsory to determine blur type
   color: 0xff00ee, // hex integers in 0xrrggbb format
   hideGestures: true, // optional, default true
   screens: ["screen1", "screen2"] // optional, default all screens
}

const textFields = {
    type: UXCamOcclusionType.OccludeAllTextFields, // compulsory to determine blur type
    screens: ["screen1", "screen2"] // optional, default all screens
}

const configuration = {
  userAppKey: 'YOUR UXCAM API KEY GOES HERE',
	occlusions: [overlay, blur, textFields]
}

RNUxcam.startWithConfiguration(configuration);
```

<br />

### Sensitive Data Occlusion Inside WebViews

When dealing with sensitive data in WebViews, it's essential to ensure that any personal or sensitive information is properly occluded to maintain user privacy. Our guide provides step-by-step instructions on how to handle occlusion within WebViews effectively.

For more information, click the button below:

[Sensitive Data Occlusion in WebViews Documentation](/docs/sensitive-views-inside-webviews)