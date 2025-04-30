---
title: Sensitive Data Occlusion In Nativescript Apps
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document outlines how to use UXCam's features to handle sensitive data
    in React Native applications, ensuring privacy compliance by occluding
    sensitive information like PII through text fields, views, and screens,
    either via the dashboard or SDK code. It emphasizes the importance of hiding
    sensitive data before app release and provides options for configuring
    occlusions, including overlays and blurs, to protect user privacy.
  robots: index
next:
  description: ''
---
In this section, we focus on how to handle sensitive data within your  React Native application using UXCam's features to ensure privacy compliance. It is essential to occlude sensitive information to protect user data like passwords, credit card numbers, or any other Personally Identifiable Information (PII).

UXCam ensures that as a controller you can use our platform and fulfil your obligations under GDPR. However, if you collect any **PII data** in your app such as email address, phone, or credit card number you should use our API to hide it.

You can choose to hide:

* **Text Fields**: Hide PII collected through text input fields
* **Views**: Hide specific views on the screen.
* **Screens:** Hide entire screens, such as payment pages

<br />

Sensitive information is hidden directly on the device by applying red boxes or blurring, meaning this data is never sent to UXCam servers. Ensure that all sensitive data is hidden before releasing your app to production.

> **Note**: When occluding sensitive data, you still have the option to record gestures for those views or screens. If you're occluding passwords or other sensitive inputs, we recommend also disabling gesture recording.

***

## Are there any elements occluded right out of the box?

Nativescript provides the following occlusion behaviour by default:

* Any text input tagged with `uxcam-occlude` or `&lt;&gt;` is occluded.

***

# Add Occlusions Directly From Your Dashboard:

You can now add occlusion rules to your app directly from your dashboard. Simply go into your app's settings in your dashboard and enable the rules you need, below is a breakdown of how to take full advantage of this feature, without having to resort to adding additional code in your app for most scenarios:

### Occlude All Screens from Dashboard

From your app's settings in the UXCam dashboard, you'll see the **video recording privacy** section, from there, you'll see the first option to either record, occlude or blur all screens in your app.

<Image align="center" alt="Blur option will also enable you to select the blur radius (strength) once selected." border={false} caption="Blur option will also enable you to select the blur radius (strength) once selected." src="https://files.readme.io/751b737-image.png" />

### Screen Specific Occlusion Rules from Dashboard

You can also customise which screens you want to apply occlusion rules to, and can create multiple rules, for example, blurring a particular screen but occluding others:

![](https://files.readme.io/6b8810f-small-Staging_-_UXCam_Dashboard.png)

### Occlude Text Input Fields from Dashboard

You can also choose to occlude all text input fields on a specific or multiple screens by simply checking the option and selecting the screens you'd like to occlude the text inputs in.

![](https://files.readme.io/253cbf0-small-Staging_-_UXCam_Dashboard.png)

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

```java
import { NSUXCam } from 'nativescript-uxcam';

// Apply an overlay to the entire screen
NSUXCam.applyOcclusion();

// Remove the overlay
NSUXCam.removeOcclusion();
```

> **NOTE:** Please keep in mind that this API will only hide `TextFields`\
> components. If you wish to hide `View` components, refer to hiding Views below.

<br />

<Image align="center" alt="2534" border={false} caption="All fields identified as text will be occluded." title="TextFields.png" src="https://files.readme.io/9caa54d-TextFields.png" width="80%" />

## Hide Sensitive View

Use it to hide specific views with sensitive information that you don't want to record.

The API parameters are:

**sensitiveView**: A View object that contains sensitive information.

```java
import { NSUXCam } from 'nativescript-uxcam';

NSUXCam.occludeSensitiveView(viewElement); // Occlude specific view element
```

## Usage from configuration object

It's also possible to pass a list of occlusions (except Sensitive View) to be applied during configuration.  For example:

```java
import { NSUXCam } from 'nativescript-uxcam';

const blur = {
  blurRadius: 10, // Optional, default is 20
  hideGestures: true, // Optional, default is true
  screens: ["screen1", "screen2"] // optional, default all screens
};

const overlay = {
  color: "#FF0000", // Color in hex format
  hideGestures: true, // Optional, default is true,
  screens: ["screen1", "screen2"] // optional, default all screens
};

const configuration = {
  userAppKey: 'YOUR UXCAM API KEY GOES HERE',
	occlusions: [overlay, blur]
}

NSUXCam.startWithConfiguration(configuration);
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

```java
import { NSUXCam } from 'nativescript-uxcam';

// Apply a blur to a screen
NSUXCam.applyBlur();

// Remove the blur
NSUXCam.removeBlur();
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

<Image align="center" alt="Blur radius customization examples" border={false} caption="Blur radius customization examples" src="https://files.readme.io/72a8f8d-Blur_Results_Comparison_-_Product_Development_-_Confluence.png" />

***

## Occlude all Text Fields

Similar to the new Overlay and Blur APIs:

```java
import { NSUXCam } from 'nativescript-uxcam';

// Apply an occlusion to all text fields
NSUXCam.occludeAllTextFields();

// Stop occluding text fields
NSUXCam.stopOccludingAllTextFields();
```

> **NOTE:** Please keep in mind that this API will only hide `TextFields`\
> components. If you wish to hide `View` components, refer to hiding Views below.

<br />

<Image align="center" alt="2534" border={false} caption="All fields identified as text will be occluded." title="TextFields.png" src="https://files.readme.io/9caa54d-TextFields.png" width="80%" />

## Hide Sensitive View

Use it to hide specific views with sensitive information that you don't want to record.

The API parameters are:

**sensitiveView**: A View object that contains sensitive information.

```coffeescript
import { NSUXCam } from 'nativescript-uxcam';

NSUXCam.occludeSensitiveView(viewElement); // Occlude specific view element
```

## Usage from configuration object

It's also possible to pass a list of occlusions (except Sensitive View) to be applied during configuration.  For example:

```java
import { NSUXCam } from 'nativescript-uxcam';

const blur = {
  blurRadius: 10, // Optional, default is 20
  hideGestures: true, // Optional, default is true
  screens: ["screen1", "screen2"] // optional, default all screens
};

const overlay = {
  color: "#FF0000", // Color in hex format
  hideGestures: true, // Optional, default is true,
  screens: ["screen1", "screen2"] // optional, default all screens
};

const configuration = {
  userAppKey: 'YOUR UXCAM API KEY GOES HERE',
	occlusions: [overlay, blur]
}

NSUXCam.startWithConfiguration(configuration);
```

<br />

### Sensitive Data Occlusion Inside WebViews

When dealing with sensitive data in WebViews, it's essential to ensure that any personal or sensitive information is properly occluded to maintain user privacy. Our guide provides step-by-step instructions on how to handle occlusion within WebViews effectively.

For more information, click the button below:

[Sensitive Data Occlusion in WebViews Documentation](/docs/sensitive-views-inside-webviews)