---
title: Android
excerpt: How to Get Started with UXCam for Android
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: screen-tagging
      title: Screen Tagging
---
So you've got your account set up, now let's make sure your Android app is equipped with the insights that UXCam can provide. This guide will take you through the first steps of integrating UXCam, sending your first session, and setting up key features. Our goal? A successful integration that sets you up for product-led growth, better usability insights, and happier users.

## What Does a Successful Integration Look Like?

With a solid integration, you'll have a complete picture of how users interact with your app. From screen journeys and user behaviours to session replays and user properties, you'll be able to understand and enhance every aspect of your product's user experience. Follow along with this quick guide, and you'll be up and running in no time.

<br />

<SimpleStepper>
  <SimpleStep header="Step 1: Install And Initialize">
    Add the UXCam SDK to your project, obtain your app key from the UXCam dashboard, and initialize the SDK in your app’s entry point.
  </SimpleStep>

  <SimpleStep header="Step 2: Customize Your Data">
    Configure screen tagging, custom events, user identities, their properties and occlusion of any sensitive views or data shown in your app.
  </SimpleStep>

  <SimpleStep header="Step 3: Test And Review">
    Run through critical user journeys in a test build to verify that sessions are recorded and events are logged correctly and iteratively refine your event tags, screen names, and occlusion rules based on the insights you uncover.
  </SimpleStep>
</SimpleStepper>

<br />

### Quick Start: Only a Couple of Lines of Code

[![pod version](https://img.shields.io/badge/Maven-3.+-green)](#)

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing user sessions in your test app.

#### Adding The Dependencies:

1. In the **module's build.gradle** file add UXCam:
   ```java
   repositories {
     maven{ 
        url 'https://sdk.uxcam.com/android/' 
     } 
   } 
   dependencies { 
        implementation 'com.uxcam:uxcam:3.+' 
   }
   ```

#### Initializing UXCam:

1. In your launcher activity, import UXCam:
   ```java
   import com.uxcam.UXCam;
   import com.uxcam.datamodel.UXConfig;
   ```

2. Now, you need to create a configuration object with desired settings and then start UXCam using this configuration object:

   ```java Kotlin
   val config = UXConfig.Builder("yourAppKey")
       .build()
   ```
   ```coffeescript Java
   UXConfig config = new UXConfig.Builder("yourAppKey").build();
   ```

   ```java
   UXCam.startWithConfiguration(config);
   ```

<br />

> 👍 As Simple As That!
>
> This will complete the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
>
> We recommend that after you've set this up and have reviewed some sessions from your tests, get to the customisation features UXCam offers, let's go to the next steps!

## Next Steps ➡️

You've successfully integrated UXCam and sent some sessions, great job! 🎉  But there's so much more you can do. Now, let's go further into setting things up.