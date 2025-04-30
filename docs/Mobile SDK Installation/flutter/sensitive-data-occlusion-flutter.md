---
title: Sensitive Data Occlusion In Flutter Apps
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document outlines how to use UXCam's features to occlude sensitive data
    in Flutter applications, ensuring privacy compliance by hiding information
    like passwords and credit card numbers using the OccludeWrapper widget or
    dashboard settings.
  robots: index
next:
  description: ''
---
In this section, we focus on how to handle sensitive data within your Flutter application using UXCam's features to ensure privacy compliance. It is essential to occlude sensitive information to protect user data like passwords, credit card numbers, or any other Personally Identifiable Information (PII).

UXCam ensures that as a controller you can use our platform and fulfil your obligations under GDPR. However, if you collect any **PII data** in your app such as email address, phone, or credit card number you should use our API to hide it.

You can choose to hide:

* **Views**: Hide specific views on the screen.
* **Screens:** Hide entire screens, such as payment pages

<br />

Sensitive information is hidden directly on the device by applying red boxes or blurring, meaning this data is never sent to UXCam servers. Ensure that all sensitive data is hidden before releasing your app to production.

> **Note**: When occluding sensitive data, you still have the option to record gestures for those views or screens. If you're occluding passwords or other sensitive inputs, we recommend also disabling gesture recording.

***

## Are there any elements occluded right out of the box?

By default, Flutter does not enable occlusion out of the box. Developers need to use the `OccludeWrapper` class to occlude sensitive elements in their application.

***

# Flutter Occlusion Wrapper

## What is it and what does it do?

With Flutter, occluding specific widgets such as Column or ListView was previously challenging. The  `OccludeWrapper` feature makes it easy to hide sensitive widgets from video recordings.

Now you might be wondering, how does it work? Well, it's pretty simple. Decide which is the widget that you want to occlude and pass it as a child to our **OccludeWrapper** container.

## Things to note

It's pretty important to mention that our SDK is the one in charge of occluding these elements, so the occlusion settings will happen in the device before the session gets uploaded to the UXCam servers.

## Example: How to Use OccludeWrapper

What do you need to do in order to implement this new feature?

1. **First of all (IMPORTANT), import the library that contains this new wrapper**

```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';
```

2. **Decide which is the widget that you would like to occlude and wrap it with an occlusion container (OccludeWrapper Widget).**

```dart
const OccludeWrapper(
   child:
   // Here goes your widget
),
```

Example on how to get a Text widget occluded

```dart
//Example
const OccludeWrapper(  
   child: Text(  
       'Sensitive data that will be occluded by the Wrapper',  
    ),  
),  
```

3. **Now you are all set, you should start getting video recordings with your widgets being occluded properly.**

# Add Occlusions Directly From Your Dashboard:

You can add screen level occlusion rules to your app directly from your dashboard. Simply go into your app's settings in your dashboard and enable the rules you need, below is a breakdown of how to take full advantage of this feature, without having to resort to adding additional code in your app for most scenarios:

### Occlude All Screens from Dashboard

From your app's settings in the UXCam dashboard, you'll see the **video recording privacy** section, from there, you'll see the first option to either record, occlude or blur all screens in your app.

<Image align="center" alt="Blur option will also enable you to select the blur radius (strength) once selected." border={false} caption="Blur option will also enable you to select the blur radius (strength) once selected." src="https://files.readme.io/751b737-image.png" />

### Screen Specific Occlusion Rules from Dashboard

You can also customise which screens you want to apply occlusion rules to, and can create multiple rules, for example, blurring a particular screen but occluding others:

![](https://files.readme.io/6b8810f-small-Staging_-_UXCam_Dashboard.png)

<br />

> 📘 **Note**
>
> **Note**
>
> Additionally, you can opt to record gestures on all blurred/occluded screens by toggling on the option
>
> ![](https://files.readme.io/33bf4ad-image.png)

### Occlusion priority:

* Screen specific overlay from Dashboard
* Screen specific blur from Dashboard
* Global blur/overlay from Dashboard that is applied to all screens
* Screen specific Overlay from SDK
* Screen specific Blur from SDK
* Global blur/overlay from SDK that is applied to all screens
* Global blur/overlay from SDK that has Record exception screens

### Limitations:

* Hiding sensitive **Views** needs to be handled from code (see [here](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view))

# Occlusion Setup from SDK Code

If you prefer to manually handle occlusions in your app or [occlude specific views](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view) instead of entire screens, read below for guidance on how to set it up.

## Occlude The Entire Screen with Overlay

You can configure different overlay options with the following:

```dart
import 'package:flutter_uxcam/uxoverlay.dart'; //Import this for Overlay

FlutterUXOverlay overlay = FlutterUXOverlay(
   		color: Colors.red,
		hideGestures: true // optional, default true
	);

FlutterUxcam.applyOcclusion(overlay); //To apply overlay
FlutterUxcam.removeOcclusion(overlay); //To remove overlay
```

**Available overlay options are:**

<p style={{fontSize: "17px"}}><code>withoutGesture(boolean withoutGesture) || hideGestures(boolean hideGestures)</code><br /></p>
Allows the user to configure wether to capture gesture in the occluded screen or not. Passing in false to this method tells the SDK to capture gestures. Default is true, so by default the gestures are not captured.

<p style={{fontSize: "17px"}}><code>screens(List screens)</code> - Use it in the configuration object<br /></p>
Allows you to define the screens where the overlay is to either be applied or not, depending on the value passed to <strong>excludeMentionedScreens(boolean excludeMentionedScreens)</strong>.

By default, if no screens are passed, the overlay is applied to all the screens unless explicitly removed. This acts as a global setting and will override all other occlusion settings defined for all screens. The occlusion must be removed to revert this action.

If screens are passed, you have the ability to either apply overlay on the mentioned screens or to exclude the mentioned screens from being overlayed.

<p style={{fontSize: "17px"}}><code>excludeMentionedScreens(boolean excludeMentionedScreens)</code><br /></p>
This option should be used in conjunction with <strong>screens(List screens)</strong>.

If the passed in value is true, it tells the SDK to exclude the mentioned screens from occlusion, while applying the occlusion to the rest of the screens in the app.

If the passed in value is false, it tells the SDK to apply occlusion only to the screens that have been passed.

Default value is false.

<Image align="center" alt="2534" border={false} caption="You'll see the desired screen completely hidden while your users navigate through it." title="Overlay.png" src="https://files.readme.io/095be49-Overlay.png" width="80%" />

## Blur The Entire Screen

Blur is an occlusion API that allows you to blur screen records of screens. This lets you obtain information regarding the state of the screen and user interaction, while also maintaining privacy in sensitive screens.

This is useful to set all the occlusion/Blur from one place of the application without having to set it individually in different screens.

You can configure different options using the following:

```dart
//Import this on the screen you are trying to Blur
import 'package:flutter_uxcam/uxblur.dart';

FlutterUXBlur blur = FlutterUXBlur(
   		blurRadius: 10,
   		blurType: BlurType.gaussian,
		hideGestures: true // optional, default true
	);

FlutterUxcam.applyOcclusion(blur); //To apply blur
FlutterUxcam.removeOcclusion(blur); //To remove blur
```

**Available blur options are:**

<p style={{fontSize: "17px"}}><code>blurRadius(int blurRadius)</code><br /></p>
This option allows you to define the blur radius to be used for blurring. The higher the value, the more blurred the resulting video is going to be.

<p style={{fontSize: "17px"}}><code>withoutGesture(boolean withoutGesture) || hideGestures(boolean hideGestures)</code><br /></p>
Same as overlay. Please refer to overlay section.

<p style={{fontSize: "17px"}}><code>screens(List screens)</code> - Use it in the configuration object<br /></p>
Same as overlay. Please refer to overlay section.

<p style={{fontSize: "17px"}}><code>excludeMentionedScreens(boolean excludeMentionedScreens)</code><br /></p>
Same as overlay. Please refer to overlay section.

<Image align="center" alt="736" border={false} caption="You'll see your desired screens with a blur on top." title="UXCam Dashboard - 24 May 2022 (1) (1).gif" src="https://files.readme.io/4b4c4ce-UXCam_Dashboard_-_24_May_2022_1_1.gif" />

**Examples on blur radius property customization:**

<Image align="center" alt="Blur radius customization examples" border={false} caption="Blur radius customisation examples" src="https://files.readme.io/72a8f8d-Blur_Results_Comparison_-_Product_Development_-_Confluence.png" />

<br />

## Hide Sensitive View

Use it to hide specific views with sensitive information that you don't want to record.

The API parameters are:

**sensitiveView**: A widget or view object that contains sensitive information.

```dart
const OccludeWrapper(     
   child:
   // Here goes your widget that you want to occlude
),
//Example  
const OccludeWrapper(  
   child: Text(  
       'Sensitive data that will be occluded by the Wrapper',  
    ),
),
```

## Usage from configuration object

It's also possible to pass a list of occlusions (except Sensitive View) to be applied during configuration.  For example:

```dart
import 'package:flutter_uxcam/uxblur.dart'; //Import this for Blurring
import 'package:flutter_uxcam/uxoverlay.dart'; //Import this for Overlay

FlutterUXBlur blur = FlutterUXBlur(
   	blurRadius: 10, 
   	blurType: BlurType.gaussian, 
	hideGestures: true, 
	screens: ["LoginScreen", "PaymentScreen"] 
);

FlutterUXOverlay overlay = FlutterUXOverlay(
   color: Colors.red, 
   hideGestures: true, 
   screens: ["LoginScreen", "PaymentScreen"]
);

FlutterUxConfig config = FlutterUxConfig(
	userAppKey: "UXCAM_APP_KEY", 
	occlusions: [blur] // can contain blur and overlay
); 

FlutterUxcam.startWithConfiguration(config);
```

<br />

### Sensitive Data Occlusion Inside WebViews

When dealing with sensitive data in WebViews, it's essential to ensure that any personal or sensitive information is properly occluded to maintain user privacy. Our guide provides step-by-step instructions on how to handle occlusion within WebViews effectively.

For more information, click the button below:

[Sensitive Data Occlusion in WebViews Documentation](/docs/sensitive-views-inside-webviews)

# Summary:

Using UXCam's occlusion features, you can ensure sensitive information in your Flutter app is hidden before it is sent to our servers. Whether using the OccludeWrapper widget or setting screen-level occlusions via the UXCam dashboard, these tools provide flexibility to meet privacy standards.