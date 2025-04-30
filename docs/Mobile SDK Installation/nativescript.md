---
title: Nativescript
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    This document provides a guide for integrating UXCam into a NativeScript
    app, detailing initial setup steps, including adding the UXCam plugin,
    importing it, and starting it with configuration, to capture user sessions
    and enhance user experience insights.
  robots: index
next:
  description: ''
---
So you've got your account set up, now let’s make sure your NativeScript app is equipped with the insights that UXCam can provide. This guide will take you through the first steps of integrating UXCam, sending your first session, and setting up key features. Our goal? A successful integration that sets you up for product-led growth, better usability insights, and happier users.

## What Does a Successful Integration Look Like?

With a solid integration, you’ll have a complete picture of how users interact with your app. From screen journeys and user behaviors to session replays and user properties, you’ll be able to understand and enhance every aspect of your product’s user experience. Follow along with this quick guide, and you’ll be up and running in no time.

### Quick Start: Only a Couple of Lines of Code

[![npm version](https://badge.fury.io/js/nativescript-uxcam.svg)](#)

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing user sessions in your test app.

#### NativeScript Integration

1. **Add UXCam to Your Project**:

   ```typescript
   tns plugin add nativescript-uxcam
   ```

2. **Import the UXCam Plugin**:

   Add the UXCam plugin code at the top of your App Component file:

   ```typescript
   import { NSUXCam } from 'nativescript-uxcam';
   ```

3. **Start UXCam in Your App**:

   Inside your component, initialise the UXCam SDK. Your **App-key** is available on the UXCam dashboard:

   ```typescript
   var config = {
       userAppKey: 'YOUR APP KEY',
   };

   NSUXCam.optIntoSchematicRecordings(); // Enable iOS screen recordings
   NSUXCam.startWithConfiguration(config); // Start UXCam with the configuration
   ```

#### Example with Angular or Vue

If you're using Angular or Vue with your NativeScript project, here's how you should integrate UXCam:

- **Vue Example**:

  ```typescript Vue
  export default Vue.extend({
      components: {
          Items,
          Browse,
          Search
      },
      mounted() {        
          var config = {
              userAppKey: 'YOUR APP KEY',
              enableAutomaticScreenNameTagging: false,
          };
          
          NSUXCam.optIntoSchematicRecordings();
          NSUXCam.startWithConfiguration(config);
      },
  });
  ```

- **Angular Example**:

  ```typescript Angular
  import { Component, OnInit } from '@angular/core';
  import { NSUXCam } from 'nativescript-uxcam';

  var config = {
      userAppKey: 'YOUR APP KEY',
  };

  @Component({
      selector: 'ns-app',
      templateUrl: 'app.component.html',
  })
  export class AppComponent implements OnInit {
      constructor() {
          NSUXCam.optIntoSchematicRecordings(); // Enable iOS screen recordings
          NSUXCam.startWithConfiguration(config);
      }
  }
  ```

> 👍 **As Simple As That!**
> 
> This will complete the integration process.  
> Your session will be shown on the dashboard within a few seconds after the app goes into the background.  
> 
> We recommend that after you've set this up and have reviewed some sessions from your tests, you explore the customisation features UXCam offers. Let's move on to the next steps!

## Next Steps ➡️

You’ve successfully integrated UXCam and sent some sessions, great job! 🎉 But there's so much more you can do. Now, let’s go further into setting things up, like identifying users, tagging sessions, and protecting sensitive data.