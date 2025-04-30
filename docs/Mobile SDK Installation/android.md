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
So you've got your account set up, now let’s make sure your Android app is equipped with the insights that UXCam can provide. This guide will take you through the first steps of integrating UXCam, sending your first session, and setting up key features. Our goal? A successful integration that sets you up for product-led growth, better usability insights, and happier users.

## What Does a Successful Integration Look Like?

With a solid integration, you’ll have a complete picture of how users interact with your app. From screen journeys and user behaviours to session replays and user properties, you’ll be able to understand and enhance every aspect of your product’s user experience. Follow along with this quick guide, and you’ll be up and running in no time.

### Quick Start: Only a Couple of Lines of Code

[![pod version](https://img.shields.io/badge/Maven-3.+-green)](#)

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing user sessions in your test app.

#### Adding The Dependencies:

1. In the **module's build.gradle** file add UXCam:

   <pre><code class="language-java">repositories {
     maven{ 
        url 'https://sdk.uxcam.com/android/' 
     } 
   } 
   dependencies { 
        implementation 'com.uxcam:uxcam:3.+' 
   }}
   </code></pre>

#### Initializing UXCam:

1. In your launcher activity, import UXCam:
   <pre><code class="language-java">import com.uxcam.UXCam;
   import com.uxcam.datamodel.UXConfig;</code>
   </pre>

2. Now, you need to create a configuration object with desired settings and then start UXCam using this configuration object:

```java Java
UXConfig config = new UXConfig.Builder("yourAppKey")
	.build();

```
```java Kotlin
val config = UXConfig.Builder("yourAppKey")
    .build()

```

```coffeescript Java/Kotlin
UXCam.startWithConfiguration(config);
```

<br />

> 👍 As Simple As That!
> 
> This will complete the integration process.  
> Your session will be shown on the dashboard within a few seconds after the app goes in the background. 
> 
> We recommend that after you've set this up and have reviewed some sessions from your tests, get to the customisation features UXCam offers, let's go to the next steps!

## Next Steps ➡️

You’ve successfully integrated UXCam and sent some sessions, great job! 🎉  But there's so much more you can do. Now, let’s go further into setting things up.