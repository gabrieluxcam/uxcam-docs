---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document provides a guide on screen tagging in Xamarin using the UXCam
    SDK, explaining both automatic and manual tagging methods to track user
    interactions effectively, with automatic tagging being easy to implement and
    manual tagging offering more customization for precise analytics.
  robots: index
next:
  description: ''
---
# Screen Tagging for MAUI and Xamarin - A Comprehensive Guide

So you've integrated the UXCam SDK, and your sessions are coming through—great! Now it's time to understand the concept of screens: what they are, how to tag them, and why tagging is crucial for understanding user behavior in your app.

## Automatic Screen Tagging - The Simple Approach

For Xamarin, UXCam's SDK provides automatic screen tagging by default. This means that screens—such as view controllers in iOS or activities in Android—are automatically captured and tagged as users navigate through them, requiring minimal integration effort from you.

Automatic tagging is an easy way to start tracking user interactions, but since Xamarin is a wrapper framework for native iOS and Android apps, some additional customization is recommended. This ensures that all screens are accurately captured, and specific user journeys are fully understood.

### Configuring Automatic Tagging

By default, UXCam automatically tags screens as they are presented. However, you can control this behavior, for example, if you want to apply different tags for certain screens or exclude some views entirely.

To enable (default) or disable automatic tagging during SDK initialization, use the following code:

```Text MAUI
// In screen names
protected override void OnAppearing()
{
base.OnAppearing();
UXCam.TagScreenName("name of screen");
}

```
```csharp Xamarin
// Configuration Example for Xamarin
UXCamConfiguration configuration = new UXCamConfiguration(
    userAppKey: "userAppKey",
    enableAutomaticScreenNameTagging: false // default is true
);
```

## Manual Screen Tagging - For In-Depth Customization

While automatic tagging is efficient, there are times when you may want more precise control over your screen names or when custom screens need to be clearly identified for better analytics. In these situations, manual screen tagging comes into play.

Manual tagging ensures that each screen gets the correct name, improving the granularity of your analytics. This is particularly useful for:

* **Assigning specific names to screens** that are more meaningful to your app's business context.
* **Tagging screens dynamically**, based on user behavior.

### Example: Manually Tagging a Screen

Below are examples of how to manually tag a screen in Xamarin for both iOS and Android.

Use the `ViewWillAppear` method for iOS and `OnResume` for Android:

```csharp Xamarin
// iOS Example

public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);
    
    // Tag the screen name for UXCam
    UXCam.TagScreenName("Home Screen Manual");
}

// Android Example

protected override void OnResume()
{
    base.OnResume();

    // Tag the screen name for UXCam
    UXCam.TagScreenName("Home Screen Manual");
}
```

### Best Practices for Screen Tagging

* **Automatic Tagging**: By default, UXCam automatically tags screens in Xamarin apps. This approach is perfect for getting started quickly and ensuring basic analytics coverage.
* **Manual Tagging**: When you need specific names for screens or want to enhance your analytics with more meaningful tags, manual tagging is easy to implement. It should be done within lifecycle methods like `ViewWillAppear` (iOS) and `OnResume` (Android).

With a mix of automatic and manual tagging, you'll gain a full understanding of how users navigate your app, giving you the insights needed for data-driven decisions.

## Summary

* **Automatic Tagging**: Convenient and suitable for simple integration.
* **Manual Tagging**: More customizable, allowing you to assign meaningful tags to screens and enhance the quality of your analytics.

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

## Next Steps ➞

You're on the right track! Now that you’ve set up screen tagging, let's move on to protecting sensitive information in your app to ensure that your users’ privacy is maintained while you continue to gain valuable insights.