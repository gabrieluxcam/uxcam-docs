---
title: Control Recording (COPY)
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
The following API allows you to control when the session gets recorded.

### Start a New Recording

Start the UXCam application after having previously stopped it to verify the API key with the server, get the settings configurations and start capturing the data according to the configuration **if you previously stopped it**.

```swift iOS
UXCam.startNewSession()
   
```
```java Android
UXCam.startNewSession()
```
```javascript React Native
RNUxcam.startNewSession()
```
```javascript Flutter
void startNewSession()
```
```csharp Xamarin
UXCam.StartNewSession()
```
```javascript Cordova
UXCam.startNewSession()
```
```coffeescript SwiftUI
UXCamCore.startNewSession()

```

### Stop Recording

UXCam automatically stops a session when the app goes to the background. Using this API you are going to be able to stop a session and upload it.

This is useful for:

- Recording a specific flow without sending the application to the background.
- Sending data (events, gestures, etc.) in a session without the closure of an app.

```swift iOS
UXCam.stopSessionAndUploadData()

//With completion handler
UXCam.stopSessionAndUploadData(_ block: (() -> Void)? = nil)
```
```java Android
UXCam.stopSessionAndUploadData();
```
```javascript React Native
RNUxcam.stopSessionAndUploadData: () => void
```
```javascript Flutter
void stopSessionAndUploadData()
```
```javascript Xamarin
void UXCam.StopSessionAndUploadData()
```
```text Cordova
UXCam.stopSessionAndUploadData: () => void
```
```coffeescript SwiftUI
UXCamCore.stopSessionAndUploadData()

//With completion handler
UXCamCore.stopSessionAndUploadData(_ block: (() -> Void)? = nil)
```

### Allow a Short Break for Another App

This API allows the user to take a short trip to another app without causing a break in the current session.

This is useful when users need to use another app to complete a certain process like:

- A login using Google, Facebook, etc.
- Navigating to another URL using an external browser.
- Taking a picture in order to complete a setup process.
- Getting the OTP and/or verification email to complete a process.
- Obtaining the confirmation of a payment through an external resource.

```swift iOS
UXCam.allowShortBreak(forAnotherApp continueSession: Bool)

//With waiting time
UXCam.setAllowShortBreakMaxDuration(duration: Double)
```
```java Android
UXCam.allowShortBreakForAnotherApp(boolean continueSession);
    
//With waiting time
UXCam.allowShortBreakForAnotherApp(int timeInMilliseconds);
```
```javascript React Native
RNUxcam.allowShortBreakForAnotherApp: (continueSession: number | boolean) => void
```
```javascript Flutter
void allowShortBreakForAnotherApp(bool continueSession)
```
```csharp Xamarin
void UXCam.AllowShortBreakForAnotherApp(bool continueSession)
    
//Android only
void UXCam.AllowShortBreakForAnotherApp(int timeInMilliseconds)
```
```java Cordova
UXCam.allowShortBreakForAnotherApp: (continueSession: boolean) => void
```
```java NativeScript
static allowShortBreakForAnotherApp: (continueSession: boolean | number) => void;

NSUXCam.allowShortBreakForAnotherApp(boolean)
```
```coffeescript SwiftUI
UXCamCore.allowShortBreak(forAnotherApp continueSession: Bool)

//With waiting time
UXCamCore.setAllowShortBreakMaxDuration(duration: Double)
```

> 📘 
> 
> The API parameters are:  
> **continueSession**: Set to TRUE to continue the current session after a short trip out to another app. Default is FALSE - stops the session as soon as the app enters the background.  
> **timeInMilliseconds**: JUST FOR Android - Time to wait before closing current session. By default the method will wait 180000ms (3 min) to end the session.  
> **duration**: JUST FOR iOS - Set to the duration of break allowed.

> 🚧 
> 
> **Important**: To avoid any inconsistencies, set **allowShortBreakForAnotherApp(true)** before navigating to another app and call **allowShortBreakForAnotherApp(false)** after returning to the app.

### Add Verification Listener

With this API you are going to be able to get the verification status when UXCam starts recording a session.

By getting this you are going to be able to:

- Send events to UXCam as soon as the session starts.
- Log the current UXCam session URL or the user URL to other 3rd party services such as Firebase.

```text iOS
Doesn't exist on iOS - use startWithKey with completion handler.
```
```java Android
UXCam.addVerificationListener(OnVerificationListener listener)

//Example:
    UXCam.addVerificationListener(new OnVerificationListener() {
        @Override
        public void onVerificationSuccess() {
            //Do Something here
        }

        @Override
        public void onVerificationFailed(String errorMessage) {
            //Do Something here
        }
    });

//To remove
UXCam.removeVerificationListener(OnVerificationListener listener);
```
```javascript React Native
import { NativeModules, NativeEventEmitter } from 'react-native';

const emitter = new NativeEventEmitter(NativeModules.RNUxcam)
this.uxcamListener = emitter.addListener('UXCam_Verification_Event', async (status) => {
    if (status.success){
        //Do Something here
    }
})

//To remove
uxcamListener.remove()
```
```text Flutter
Not supported
```
```csharp Xamarin
//Android only - use callback action for iOS.
UXCam.AddVerificationListener(OnVerificationListener listener)

//Example:
    UXCam.AddVerificationListener(new IOnVerificationListener());
    class IOnVerificationListener : Java.Lang.Object, Com.UXCam.IOnVerificationListener
    {
        public void OnVerificationFailed(string p0)
        {
            //Do Something here
        }

        public void OnVerificationSuccess()
        {
            //Do Something here
        }
    }

//To remove
UXCam.RemoveVerificationListener(OnVerificationListener listener);
```
```text Cordova
Not supported
```