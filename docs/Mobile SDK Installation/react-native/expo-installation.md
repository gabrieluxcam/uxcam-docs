---
title: Expo Installation
excerpt: This is a quick guide on how to integrate UXCam into your Expo application.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
Expo is a universal platform used for building native applications for both Android and iOS. But as good as it sounds, it has its own limitations. Expo does not support native modules so previously, in order to add libraries such as UXCam you will need to detach your project.

But the sun always shines after the storm, and now with the introduction of [EAS](https://expo.dev/eas), which is simply another version of Expo which will allow you to add any custom native code to your build, so in other words, detaching your Expo project is no longer necessary.

So in case you have an Expo application and want to check the possibility of integrating UXCam in your application, please just follow the next steps:

## Integration

1. Install the latest EAS CLI - For this you can just do the following:
   <pre><code class="language-java"> npm install -g eas-cli
   </code></pre>

2. Configure your project to run under the EAS build - For this you can do the following to configure your iOS and Android Projects:
   <pre><code class="language-java"> eas build:configure 
   </code></pre>

3. Add UXCam in your application - You can follow the instructions listed in [here](https://developer.uxcam.com/docs/react-native).

4. Run your build - For this you can do:
   <pre><code class="language-java">//Android 
   eas build --platform android 
   //iOS 
   eas build --platform ios
   </code></pre>

> 👍
>
> This will complete the integration process. Your session will be shown on the dashboard within a few seconds after the app goes in the background.

For more reference on how to add EAS into your Expo application, please visit the following link: [https://docs.expo.dev/build/setup/](https://docs.expo.dev/build/setup/)

Please reach out to our Customer Success Team at [team@uxcam.com](mailto:team@uxcam.com) if you need any additional help.