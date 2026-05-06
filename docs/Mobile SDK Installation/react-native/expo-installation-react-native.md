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
   ```bash
   npm install -g eas-cli
   ```

2. Configure your project to run under the EAS build - For this you can do the following to configure your iOS and Android Projects:
   ```bash
   eas build:configure
   ```

3. Add UXCam in your application - You can follow the instructions listed in [here](https://developer.uxcam.com/docs/react-native).

4. Run your build - For this you can do:
   ```bash
   //Android
   eas build --platform android
   //iOS
   eas build --platform ios
   ```

> 👍
>
> This will complete the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.

For more reference on how to add EAS into your Expo application, please visit the following link: [https://docs.expo.dev/build/setup/](https://docs.expo.dev/build/setup/)

Please reach out to our Customer Success Team at [team@uxcam.com](mailto:team@uxcam.com) if you need any additional help.

## Why Expo Go Doesn't Work

UXCam uses native modules (Objective-C/Swift on iOS, Java/Kotlin on Android) that Expo Go cannot load. You **must** use a [development build](https://docs.expo.dev/develop/development-builds/introduction/) or [EAS Build](https://docs.expo.dev/build/introduction/) to use UXCam.

## Setting Up with EAS Build

### 1. Install the EAS CLI

```bash
npm install -g eas-cli
eas login
```

### 2. Configure `eas.json`

If you don't already have one, create `eas.json` in your project root:

```json
{
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "production": {}
  }
}
```

### 3. Create a Development Build

```bash
eas build --profile development --platform ios
# or
eas build --profile development --platform android
```

### 4. Install and Run

Once the build completes, install it on your device/simulator and start the development server:

```bash
npx expo start --dev-client
```

UXCam will now initialize correctly with native module access.

## Common Expo Issues

| Issue | Solution |
|-------|----------|
| `RNUxcam is null` or `undefined` | You're running in Expo Go. Switch to a development build. |
| Build fails with missing native module | Run `npx expo prebuild` then rebuild. |
| Sessions not appearing after EAS build | Ensure your app key is correct and the app goes to background to trigger upload. |