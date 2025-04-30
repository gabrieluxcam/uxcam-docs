---
title: React Native
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
So you've got your account set up, now let's make sure your React Native app is equipped with the insights that UXCam can provide. This guide will take you through the first steps of integrating UXCam, sending your first session, and setting up key features. Our goal? A successful integration that sets you up for product-led growth, better usability insights, and happier users.

## What Does a Successful Integration Look Like?

With a solid integration, you'll have a complete picture of how users interact with your app. From screen journeys and user behaviours to session replays and user properties, you'll be able to understand and enhance every aspect of your product's user experience. Follow along with this quick guide, and you'll be up and running in no time.

### Quick Start: Only a Couple of Lines of Code

![npm version](https://badge.fury.io/js/react-native-ux-cam.svg)

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing user sessions in your test app.

#### React Native Integration

1. To add UXCam to your project:
   ```bash
   yarn add react-native-ux-cam

   //If you use npm instead of yarn
   npm i react-native-ux-cam
   ```

2. Add imports and create the configuration object in your App.js when your app starts. Your **App-key** is available on the UXCam dashboard
   ```javascript
   import RNUxcam from 'react-native-ux-cam';

   RNUxcam.optIntoSchematicRecordings(); // Add this line to enable iOS screen recordings
   const configuration = {
       userAppKey: 'YOUR API KEY',
       enableAutomaticScreenNameTagging: false,
       enableImprovedScreenCapture: true
    }
   RNUxcam.startWithConfiguration(configuration);
   ```

> 👍 As Simple As That!
>
> This will complete the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
>
> We recommend that after you've set this up and have reviewed some sessions from your tests, get to the customisation features UXCam offers, let's go to the next steps!

## Next Steps ➡️

You've successfully integrated UXCam and sent some sessions, great job! 🎉 But there's so much more you can do. Now, let's go further into setting things up.