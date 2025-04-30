---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document explains how to use UXCam's SDK for automatic and manual screen
    tagging in iOS apps to track user behavior, recommending automatic tagging
    for simplicity and offering manual tagging for more detailed customization.
  robots: index
next:
  description: ''
---
So you've integrated the SDK and have some sessions going already, good! Now it's time to dig into screens—what they are, how to tag them, and why it matters for understanding user behaviour in your app.

## Automatic Screen Tagging - The Recommended, Simple Approach

For iOS, UXCam's SDK automatically tags screens (i.e., view controllers), and this is the recommended default behavior. Automatic tagging means that each view controller your user navigates to is recorded automatically, without requiring you to manually intervene. This gives you the advantage of capturing user activity with minimal integration effort.

### Tagging View Controllers

By default, UXCam will automatically tag your view controllers as they are presented. If you want more granular control, such as tagging certain view controllers differently or excluding specific views, you can manage this manually.

To enable (default) or disable automatic tagging during SDK initialisation, use the following code:

```coffeescript Swift
let configuration = UXCamConfiguration(appKey: "YourAppKey")
configuration.enableAutomaticScreenNameTagging = true // True by default. Set to false if you want to disable automatic screen tagging.
UXCam.start(with: configuration)`
```

As for enabling or disabling Fragment Based Tagging, it can be done trough the options in your UXCam Dashboard:

\-insert image here-

## Manual Screen Tagging - For In Depth Customisation

While we recommend automatic tagging, you may sometimes need more precise control over your screen names or want to ensure custom screens are clearly identified in your analytics. In these cases, you can use manual screen tagging.

**Example**: **Tagging a View Controller Manually**\
To tag a screen in a view controller, use the following code in the `viewWillAppear` method of your controller:

```coffeescript Swift
UXCam.tagScreenName(_ screenName: String)

//Example  
override open func viewWillAppear(_ animated: Bool) {
      super.viewWillAppear(animated)
      UXCam.tagScreenName("Home Screen Manual")
}
```

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

> 👍 Summary:
>
> **Automatic Tagging:** By default, UXCam automatically tags screens in iOS apps, which we recommend as the simplest and most efficient approach.
>
> **Manual Tagging:** When you need specific names for screens or want to add more meaningful tags for your analytics, manual tagging is straightforward and can be done in the lifecycle methods like `viewWillAppear`

With this approach, you'll be well-equipped to make data-driven decisions based on how users move through your app's screens.

<br />

## Next Steps ➡️

<br />

You're on a roll! You've now set up some of the most important features from UXCam to get full insights already, but let's dive next into protecting sensitive information.
