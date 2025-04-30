---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document explains the importance of manual screen tagging in Cordova for
    precise analytics, providing a simple method to tag screens using a line of
    code, and highlights the need for manual tagging due to the lack of
    automatic tagging in Cordova. It also mentions the challenges of tagging
    WebViews and offers a guide for effective tagging.
  robots: index
next:
  description: ''
---
So you've integrated the SDK and have some sessions going already, good! Now it's time to dig into screens—what they are, how to tag them, and why it matters for understanding user behaviour in your app.

## Manual Screen Tagging - For In-Depth Customisation

While on native frameworks we recommend automatic tagging, Cordova requires manual tagging to ensure precise control over your screen names and to clearly identify custom screens in your analytics. For this reason, in Cordova, you need to use manual screen tagging.

> Please make sure to disable automatic screen name tagging where applicable and use manual tagging for accurate analytics.

<br />

**As simple as a line of code:**

```javascript
UXCam.tagScreenName(screenName);
```

**Example**: **The Recommended Place to Tag**\
To tag a screen in Cordova, place the above code where each screen view is triggered to ensure the screen name is updated accordingly.

```javascript
// Example: Tagging the home screen
UXCam.tagScreenName('Home Screen');
```

<br />

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

> 👍 Summary:
>
> **Automatic Tagging:** Not available in Cordova due to the nature of how activities/screens are managed.
>
> **Manual Tagging:** For specific screen names or more meaningful tags, manual tagging is straightforward and can be added at any point where screen views are handled.

With this approach, you'll be well-equipped to make data-driven decisions based on how users move through your app's screens.

<br />

## Next Steps ➞

<br />

You're on a roll! You've now set up some of the most important features from UXCam to get full insights already, but let's dive next into protecting sensitive information to ensure the privacy of your users.
