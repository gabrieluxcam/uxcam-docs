---
title: Firebase Google Analytics
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
```markdown
<strong>Integrate UXCam with Firebase Google Analytics to get more insights about your events and better analyze your data.</strong>

Even with tons of data from events triggered in your app, sometimes it’s hard to know why your user’s made some decisions, why they’re not adopting the new features, or not using your app the way you expected them to. While Firebase can provide you with quantitative data, by sending and connecting the events to UXCam, you’ll have a better understanding of your user’s behavior.

Connect <strong>UXCam with Firebase</strong> to have your Session’s and User's URLs on each of your Firebase Events and easily watch the video recording. You can also analyze the whole <strong>user experience</strong> to have more insights and <strong>pinpoint roadblocks</strong> on your user’s journey.

## Why integrate with Firebase?

***

1. Have more <strong>consistency in your data</strong> by sending the events to Firebase and UXCam.
2. Understand the <strong>reasons behind</strong> all your Firebase data, better analyze your user’s behavior, and address problems more precisely. 

## How to do it?

***

You need to have a Firebase Google Analytics account and a UXCam account. 

### In Firebase:

1. On your Firebase project Dashboard, under the Analytics section click on Custom Definitions and then go to “<strong>Create custom dimensions</strong>”.<br/>
2. Give a descriptive name of the dimension (Event) that you want to link to UXCam.<br/>
3. Add a parameter to this dimension in “<strong>Event parameter</strong>” (here you will add your UXCam URL) with a name that you desire, i.e. UXCam\_links in example below.<br/>

![](https://files.readme.io/b16e4d7-ezgif.com-gif-maker_6_1.gif "ezgif.com-gif-maker (6) (1).gif")

### In UXCam:

1. Please make sure that the Event name that you use in UXCam and the Event Parameter are the same that you’ve created on Firebase.

2. Following our example it should be: Event: ‘<strong>purchase\_completed</strong>’ and event parameter: ‘<strong>UXCam\_links</strong>’

```swift
UXCam.start(withKey: "APP_KEY") { (started) in
    if started{
        //Add any event name or parameter name you prefer
        if let sessionURL = UXCam.urlForCurrentSession(){
            Analytics.logEvent("purchase_completed", parameters: ["UXCam_links": sessionURL])
        }
    }
}
```
```objectivec
[UXCam startWithKey:@"APP_KEY" buildIdentifier:nil completionBlock:^(BOOL started) {
    if (started){
        //Add any event name or parameter name you prefer
        [FIRAnalytics logEventWithName:@"purchase_completed" parameters:@{@"UXCam_links":[UXCam urlForCurrentSession]}];
    }
}];
```
```java Android
// Inside onCreate method of all activity that is an entry point to your app add
mFirebaseAnalytics = FirebaseAnalytics.getInstance(this);
UXCam.startWithConfiguration(config);

UXCam.addVerificationListener(new OnVerificationListener() {
    @Override
    public void onVerificationSuccess() {
        Bundle bundle = new Bundle();
        bundle.putString("UXCam_links", UXCam.urlForCurrentSession());
        mFirebaseAnalytics.logEvent("purchase_completed", bundle);
    }

    @Override
    public void onVerificationFailed(Strings) { }
    
});
```
```go Flutter
final FirebaseAnalytics analytics;
//Make sure the session was started.
FlutterUxcam.urlForCurrentSession().then((value) {
    analytics.logEvent(name: "purchase_completed", parameters: {"UXCam_links": value});
});
```
```javascript React Native
import { NativeModules, NativeEventEmitter } from 'react-native';

RNUxcam.startWithConfiguration(configuration);

//Setup listener
const emitter = new NativeEventEmitter(NativeModules.RNUxcam);
//Use this.uxcamEvent.remove() to remove listener
this.uxcamEvent = emitter.addListener('UXCam_Verification_Event', async () => {
    //Get user or session URL
    const sessionURL = await RNUxcam.urlForCurrentSession();
    await analytics().logEvent('purchase_completed', {
      UXCam_links: sessionURL, // here we send the url to Firebase as previously set up.
    });
    //Add user or session URL to your other services
});
```

## Find your Sessions' and Users' URLs in Firebase:

***

When **analyzing your Events on Firebase** you can see the custom parameters and all the UXCam URLs for that event, so you can export all these data or just go to the URL and watch the sessions directly on UXCam Dashboard to understand how the event was triggered. 

> 📘
>
> We suggest send the same events that you’ve on Firebase to UXCam, to have more consistency in your data and collect more insights into your users’ behavior. **[Send Events to UXCam →](https://uxcam-documentation.readme.io/docs/send-events)**

![](https://files.readme.io/348ec86-Firebase_k-base_article.png "Firebase k-base article.png")

### Note:

Events in Firebase can take up to 24 hours to appear in the dashboard, but if you want to make sure that you are logging the events properly, run the following:

```shell Android
adb shell setprop debug.firebase.analytics.app "yourpackagename"
```

This will enable the **DebugView** that is also located under the Analytics section in your firebase console and will let you see if the events are being properly logged.
```