---
title: Sensitive Data Occlusion In Xamarin / MAUI Apps
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document outlines how to use UXCam's features to handle sensitive data
    in Xamarin applications, emphasizing the importance of occluding Personally
    Identifiable Information (PII) to ensure privacy compliance, and provides
    guidance on setting up occlusions via the UXCam dashboard or SDK code.
  robots: index
next:
  description: ''
---
In this section, we focus on how to handle sensitive data within your Xamarin application using UXCam’s features to ensure privacy compliance. It is essential to occlude sensitive information to protect user data like passwords, credit card numbers, or any other Personally Identifiable Information (PII).

UXCam ensures that as a controller, you can use our platform and fulfill your obligations under GDPR. However, if you collect any **PII data** in your app, such as email address, phone, or credit card number, you should use our API to hide it.

You can choose to hide:

* **Texts**: When you only collect PII data with text fields.
* **Screen Views**: When you need to hide a specific section of your screen.
* **Screens**: When you need to hide the whole screen, e.g., payment screens.

Sensitive information will be hidden under red boxes or blurred screens on the device before rendering the video and therefore never sent to UXCam. Make sure that all this information is hidden before releasing your app to production to ensure your users’ PII is never recorded.

**Note**: When hiding sensitive information, you still have the option to record gestures for that view or screen. However, if you’re hiding passwords or other sensitive inputs, we recommend also disabling gesture recording.

***

## Are There Any Elements Occluded Right Out of the Box?

**iOS**

* Occlusion of text fields based on the `UITextContentType` property. If the text field has any of `password`, `creditcardnumber`, `newpassword`, or `onetimecode`, it will be occluded by default, regardless of if it’s shown at some point in the app.

**Android**

* Password fields with `android:inputType="textPassword"` or `InputType.TYPE_TEXT_VARIATION_PASSWORD` are occluded.

***

# Add Occlusions Directly From Your Dashboard

You can now add occlusion rules to your app directly from your dashboard. Simply go into your app's settings in your dashboard and enable the rules you need. Below is a breakdown of how to take full advantage of this feature without having to add additional code in your app for most scenarios:

### Occlude All Screens from Dashboard

From your app's settings in the UXCam dashboard, you'll see the **video recording privacy** section. From there, you'll see the first option to either record, occlude, or blur all screens in your app.

![Blur option will also enable you to select the blur radius (strength) once selected.](https://files.readme.io/751b737-image.png)

### Screen Specific Occlusion Rules from Dashboard

You can also customize which screens you want to apply occlusion rules to and create multiple rules. For example, you may blur a particular screen but occlude others:

![UXCam Dashboard](https://files.readme.io/6b8810f-small-Staging_-_UXCam_Dashboard.png)

### Occlude Text Input Fields from Dashboard

You can also choose to occlude all text input fields on specific or multiple screens by simply checking the option and selecting the screens you'd like to occlude the text inputs in:

![UXCam Dashboard - Occlude Text Inputs](https://files.readme.io/253cbf0-small-Staging_-_UXCam_Dashboard.png)

> 📜 **Additionally**, you can opt to record gestures on all blurred/occluded screens by toggling on the option:
>
> ![](https://files.readme.io/33bf4ad-image.png)

## Dashboard Occlusion Limitations

### Occlusion Priority

* Screen-specific overlay from Dashboard.
* Screen-specific blur from Dashboard.
* Global blur/overlay from Dashboard applied to all screens.
* Screen-specific overlay from SDK.
* Screen-specific blur from SDK.
* Global blur/overlay from SDK applied to all screens.
* Global blur/overlay from SDK with record exception screens.

### Limitations

* Hiding sensitive **Views** needs to be handled from code (see [here](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view)).

# Occlusion Setup from SDK Code

If you prefer to manually handle occlusions in your Xamarin app or [occlude specific views](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view) instead of entire screens, read below for guidance on how to set it up.

## Occlude The Entire Screen with Overlay

You can configure different overlay options with the following:

```csharp Xamarin
// Xamarin Example - Apply Overlay
UXCamOverlaySetting overlay = new UXCamOverlaySetting (UIColor.Yellow);

UXCam.applyOcclusion(overlay); // Apply overlay
UXCam.removeOcclusion(overlay); // Remove overlay
```

## Blur The Entire Screen

Blur is a new occlusion API that allows you to blur screen recordings. This lets you obtain information regarding the state of the screen and user interaction while maintaining privacy on sensitive screens.

```csharp Xamarin
// Xamarin Example - Apply Blur
UXCamBlurSetting blur = new UXCamBlurSetting (10);

UXCam.applyOcclusion(blur); // Apply blur
UXCam.removeOcclusion(blur); // Remove blur
```

**Available Blur Options Are:**

* `blurRadius(int blurRadius)`: Define the blur radius to be used. The higher the value, the more blurred the resulting video will be.
* `withoutGesture(boolean withoutGesture) || hideGestures(boolean hideGestures)`: Configure whether to capture gestures in the occluded screen or not. Default is true, so gestures are not captured.
* `screens(List<String> screens)`: Define the screens where the overlay is applied or excluded, depending on `excludeMentionedScreens`.
* `excludeMentionedScreens(boolean excludeMentionedScreens)`: Use in conjunction with `screens`. If true, exclude the mentioned screens from occlusion while applying occlusion to the rest. Default is false.

## Hide Sensitive View

Use this method to hide specific views with sensitive information that you don't want to record.

```csharp Xamarin
// Xamarin Example - Occlude Sensitive View
void UXCam.OccludeSensitiveView(View sensitiveView);
```

## Usage from Configuration Object

It is also possible to pass a list of occlusions (except Sensitive View) to be applied during configuration:

```csharp Xamarin
// Xamarin Example - Configuration with Occlusion
UXCamBlurSetting blur = new UXCamBlurSetting(10);
UXCamConfiguration configuration = new UXCamConfiguration("User API Key");

configuration.Occlusion = new UXCamOcclusion(blur);

UXCam.startWithConfiguration(configuration);
```

<br />

### Sensitive Data Occlusion Inside WebViews

When dealing with sensitive data in WebViews, it's essential to ensure that any personal or sensitive information is properly occluded to maintain user privacy. Our guide provides step-by-step instructions on how to handle occlusion within WebViews effectively.

For more information, click the button below:

[Sensitive Data Occlusion in WebViews Documentation](/docs/sensitive-views-inside-webviews)
