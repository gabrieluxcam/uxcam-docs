---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document explains the importance of manual screen tagging in
    NativeScript for precise analytics, recommending tagging screens in the
    `onNavigatedTo` lifecycle event, and provides guidance on tagging WebViews
    for better understanding of user behavior.
  robots: index
next:
  description: ''
---
So you've integrated the SDK and have some sessions going already, good! Now it's time to dig into screens—what they are, how to tag them, and why it matters for understanding user behavior in your app.

## Manual Screen Tagging - For In-Depth Customization

While on native frameworks we recommend automatic tagging, NativeScript behaves differently, which requires more precise control over your screen names to ensure custom screens are clearly identified in your analytics. For this reason, in NativeScript, you need to use manual screen tagging.

> Please make sure to set `enableAutomaticScreenNameTagging`: **`false`**

**As simple as a line of code:**

```typescript
NSUXCam.tagScreenName('Home Screen');
```

**Example**: **The Recommended Place to Tag**  
To tag a screen in NativeScript, we recommend using the following code in the `onNavigatedTo` lifecycle event of your page:

```typescript
import { NSUXCam } from 'nativescript-uxcam';
import { Page } from '@nativescript/core';

export class SomePage {
    constructor(private page: Page) {
        this.page.on(Page.navigatedToEvent, () => {
            NSUXCam.tagScreenName("Home Screen");
        });
    }
}
```

This approach ensures that every time a user navigates to this page, it's correctly tagged in your analytics.

<br />

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

> 👍 Summary:
> 
> **Automatic Tagging:** Limited due to NativeScript's lifecycle events.
> 
> **Manual Tagging:** Provides specific names for screens or allows more meaningful tags for your analytics. Manual tagging can be done in lifecycle methods like `onNavigatedTo` to make sure it's tagged every time the screen appears.

With this approach, you'll be well-equipped to make data-driven decisions based on how users move through your app's screens.

## Next Steps ➡️

You're on a roll! You've now set up some of the most important features from UXCam to get full insights already, but let's dive next into protecting sensitive information.