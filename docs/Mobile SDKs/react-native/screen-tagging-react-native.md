---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document explains the importance of manual screen tagging in React
    Native for better analytics, recommending the use of `useFocusEffect()` for
    tagging screens and providing guidance on tagging WebViews, as automatic
    tagging is inconsistent in React Native.
  robots: index
next:
  description: ''
---
So you've integrated the SDK and have some sessions going already, good! Now it's time to dig into screens—what they are, how to tag them, and why it matters for understanding user behaviour in your app.

## Manual Screen Tagging - For In Depth Customisation

While on native frameworks we recommend automatic tagging, React Native does not behave in the same manner  so you are required to add more precise control over your screen names or ensure custom screens are clearly identified in your analytics. For this reason, in React Native you need to use manual screen tagging.

> Please make sure to set `enableAutomaticScreenNameTagging`: **`false`**

<br />

**As simple as a line of code:**

```coffeescript React Native
RNUxcam.tagScreenName: (screenName: string) => void
```

**Example**: **The Recommended Place to Tag**\
To tag a screen in React Native, we recommend to use the following code in the `useFocusEffect()` method of your component:

```coffeescript Android
  useFocusEffect(() => {
    RNUxcam.tagScreenName('Home Screen');
  });
```

**Example: Tagging a Fragment Manually**\
Similarly, to tag a fragment, place the code in the `onResume()` method of the fragment:

<br />

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

> 👍 Summary:
>
> **Automatic Tagging:** Inconsistent and limited due to React Native's handling of activities/controllers
>
> **Manual Tagging:** You need specific names for screens or want to add more meaningful tags for your analytics, manual tagging is straightforward and can be done in the lifecycle methods like  `useFocusEffect` to make sure it's tagged every time it shows up

With this approach, you'll be well-equipped to make data-driven decisions based on how users move through your app's screens.

## Next Steps ➡️

<br />

You're on a roll! You've now set up some of the most important features from UXCam to get full insights already, but let's dive next into protecting sensitive information.
