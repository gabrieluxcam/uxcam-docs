---
title: Firebase Crashlytics
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
Integrate <strong>UXCam with Firebase Crashlytics</strong> to get more insights about your crashes, easily understand what happened before the crash, and compare the crash logs to have a better comprehension of the issue. 

<strong>UXCam automatically detects Crashes and UI Freeze’s </strong>, you can easily find sessions with them, watch the recording to see what triggered the crash and see the logs or share them with your developers to deeply analyze the problem. However, if you’re using Crashlytics you can connect it with UXCAm to have your Session’s URLs on each of your Crashes Events and easily watch the video recording. You can also analyze the whole experience to understand what led to the crash, and to find the replication steps. 

To read more about UXCam Crash and UI Freezes logs check this [page](https://help.uxcam.com/hc/en-us/articles/360045884471-ANR-s-Events).

***

## How to Do It?

You need to have a Firebase Crashlytics account and a UXCam account. 

```swift
//Import UXCam and Crashlytics header at the top of your AppDelegate.swift
import UXCam
import Firebase
import FirebaseCrashlytics

// Add this call as the first line of your application:didFinishLaunchingWithOptions: method
FirebaseApp.configure()

UXCam.start(with: configuration) { started in
    if started{
        if let sessionURL = UXCam.urlForCurrentSession(){
            Crashlytics.crashlytics().setCustomValue(sessionURL, forKey: "UXCam: Session Recording link")
        }
    }
}
```
```objectivec
// Import UXCam and Crashlytics header at the top of your AppDelegate.m
#import <FirebaseCore/FirebaseCore.h>
#import <FirebaseCrashlytics/FirebaseCrashlytics.h>
#import <UXCam/UXCam.h>

// Add this call as the first line of your
// application:didFinishLaunchingWithOptions: method
[FIRApp configure];

[UXCam startWithKey:@"APP-KEY" buildIdentifier:nil completionBlock:^(BOOL started) {
    if (started){
        NSString* sessionURL = [UXCam urlForCurrentSession];
        [[FIRCrashlytics crashlytics] setCustomValue:sessionURL forKey:@"UXCam: Session Recording link"];
    }
}];
```
```java Android
// Inside onCreate method of all activity that is an entry point to your app add
UXCam.startWithConfiguration(config);

UXCam.addVerificationListener(new OnVerificationListener() {
    @Override
    public void onVerificationSuccess() {
        FirebaseCrashlytics.getInstance().setCustomKey("UXCam: Session Recording link", UXCam.urlForCurrentSession());
    }

    @Override
    public void onVerificationFailed(String errorMessage) { }
    
});
```
```javascript React Native
//Import the following at the top of your App.js
import RNUxcam from 'react-native-ux-cam';
import crashlytics from '@react-native-firebase/crashlytics';
import { NativeModules, NativeEventEmitter } from 'react-native';

// Add the verification listener by doing the following
const emitter = new NativeEventEmitter(NativeModules.RNUxcam)
  this.uxcamListener = emitter.addListener('UXCam_Verification_Event', async (status) => {
    if (status.success) {
      let sessionUrl = await RNUxcam.urlForCurrentSession();
      crashlytics().setAttribute('uxcamId', sessionUrl);

    }
})
```
```go Flutter
//Import UXCam and Crashlytics at the top of your main.dart
import 'package:firebase_crashlytics/firebase_crashlytics.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';

// Inside your root widget build
FlutterUxcam.startWithConfiguration(config).then((value) async {
    if (value == true) {
        String sessionUrl = await FlutterUxcam.urlForCurrentSession();
        Crashlytics.instance.setString("UXCam: Session Recording link", sessionUrl);
    }
});
```

***

## UXCam URL in Firebase Crashlytics

You can view UXCam sessions' and users' link in the <strong>Keys</strong> section of your crashed session within Firebase console.

You can then analyze the session and video recording where the Crash happened to have more context on the Crash and see how this crash affected the user journey. Furthermore, you can analyze the user behavior after experiencing a crash in your app by checking his/her next sessions.

![](https://files.readme.io/a8ca8a7-Crashlytics-k-base.png "Crashlytics-k-base.png")
