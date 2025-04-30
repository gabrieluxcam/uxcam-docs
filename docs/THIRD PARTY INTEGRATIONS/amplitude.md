---
title: Amplitude
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
<strong>Integrate UXCam with Amplitude to gather more insights about your events and users’ experience.</strong>

Whether you are using Amplitude to follow up your events, analyze your user journey, or track conversions, you can connect it with UXCam to <strong>get the context behind each user interaction</strong> with your app by <strong>attaching your users’ and sessions’ URLs</strong> to each Amplitude’s Event. That way you will be able to analyze more in-depth the reason behind the decisions of your users, easily identify bugs, understand what actions specifically led to conversion, and precisely pinpoint roadblocks on your user’s journey.

We highly recommend sending the same Events to UXCam to have more consistency in your data.  
[Read more about our Event Analytics here →](https://help.uxcam.com/hc/en-us/articles/360050286872)

The following guide explains how to associate a UXCam session URL with Amplitude event enabling you to see a step by step session replay for your every event.

For more information about 3rd party integration and best practices, visit our other 3rd party integration guide.

## Integrate With Amplitude:

1. [Set user identity](#set-user-identity)
2. [Track UXCam session](#track-uxcam-session)
3. [UXCam session URL in Amplitude](#uxcam-session-url-in-amplitude)
4. [Log Amplitude metadata with UXCam](#log-amplitude-metadata-with-uxcam)

### Set User Identity

If your app requires registered user login, we highly encourage you to set user identity for UXCam and Amplitude. By doing so, you can easily filter all the sessions for the user from UXCam user dashboard.

![](https://files.readme.io/6d169fb-FOR_AMPLITUDE.png "FOR AMPLITUDE.png")

##### How To Do It:

```swift
//Set user identity for amplitude
Amplitude.instance()?.setUserId("user@domain.com", startNewSession: false)

//Set user identity for UXCam
UXCam.setUserIdentity("user@yourdomain.com")
```
```objectivec
//Set user identity for amplitude
[[Amplitude instance] setUserId:@"user@yourdomain.com" startNewSession:NO];

//Set user identity for UXCam
[UXCam setUserIdentity:@"user@yourdomain.com"];
```
```java Android
//Set user identity for amplitude
Amplitude.getInstance().setUserId("use@yourdomain.com");

//Set user identity for UXCam
UXCam.setUserIdentity("user@yourdomain.com")
```
```javascript React Native
//Set user identity for amplitude
amplitude.getInstance().setUserId("user@yourdomain.com");

//Set user identity for UXCam
RNUxcam.setUserIdentity("user@yourdomain.com")
```
```go Flutter
//Set your amplitude instance
final Amplitude analytics = Amplitude.getInstance(instanceName: "project");

//Set user identity for amplitude
analytics.setUserId("user@yourdomain.com");

//Set user identity for UXCam
FlutterUxcam.setUserIdentity("user@yourdomain.com")
```

### Track UXCam session

You can attach a UXCam session URL to Amplitude on UXCam session start or conditionally when a certain event occurs.

```swift
//tracking on session start
UXCam.start(withKey: "APP-KEY", buildIdentifier: nil) { (status) in
    if (status){
        if let sessionURL = UXCam.urlForCurrentSession() {
            Amplitude.instance()?.logEvent("uxcam", withEventProperties: ["session_url" : sessionURL])
        }
        
        if let userURL = UXCam.urlForCurrentUser() {
            Amplitude.instance()?.setUserProperties(["uxcam_url": userURL])
        }
    }
}

//tracking conditionally
if (purchased){
    if let sessionURL = UXCam.urlForCurrentSession() {
        Amplitude.instance()?.logEvent("purchased", withEventProperties: ["uxcam_session_url" : sessionURL])
    }
}
```
```objectivec
//tracking on session start
[UXCam startWithKey:@"APP-KEY" buildIdentifier:nil completionBlock:^(BOOL started) {
    if (started){
        NSString* sessionURL = [UXCam urlForCurrentSession];
        NSString* userURL = [UXCam urlForCurrentUser];
        
        [[Amplitude instance] logEvent:@"uxcam" withEventProperties:@{ @"session_url": sessionURL }];
        
        [[Amplitude instance] setUserProperties: @{@"uxcam_url": userURL}];
    }
}];

//tracking conditionally
if (purchased) {
    NSString* sessionURL = [UXCam urlForCurrentSession];
    [[Amplitude instance] logEvent:@"purchased" withEventProperties:@{ @"uxcam_session_url": sessionURL }];
}
```
```java Android
// Inside onCreate method of all activity that is an entry point to your app add
UXCam.startWithKey("APP-KEY");
UXCam.addVerificationListener(new UXCam.OnVerificationListener() {
    @Override
    public void onVerificationSuccess() {
        //Tag Amplitude events with UXCam recording URLS. Example:
        JSONObject eventProperties = new JSONObject();
        JSONObject userProperties = new JSONObject();
        try {
            eventProperties.put("session_url", UXCam.urlForCurrentSession());
            userProperties.put("uxcam_url", UXCam.urlForCurrentUser());
        } catch (JSONException exception) {
        }
        Amplitude.getInstance().logEvent("uxcam", eventProperties);
        //Tag Amplitude profile with UXCam user URLS. Example:
        Amplitude.getInstance().setUserProperties(userProperties);
    }
    @Override
    public void onVerificationFailed(String errorMessage) { }
});

//tracking conditionally
if (purchased) {
    JSONObject eventProperties = new JSONObject();
    try {
        eventProperties.put("uxcam_session_url", UXCam.urlForCurrentSession());
    } catch (JSONException exception) { }
    Amplitude.getInstance().logEvent("purchased", eventProperties);
}
```
```javascript React Native
import { NativeModules, NativeEventEmitter } from 'react-native';
    
RNUxcam.optIntoSchematicRecordings();
RNUxcam.startWithKey("your app key");
    
//Setup listener
const emitter = new NativeEventEmitter(NativeModules.RNUxcam);
//Use this.uxcamEvent.remove() to remove listener
this.uxcamEvent = emitter.addListener('UXCam_Verification_Event', async () => {
    const sessionURL = await RNUxcam.urlForCurrentSession();
    const userURL = await RNUxcam.urlForCurrentUser();
    amplitude.getInstance().logEvent("uxcam", {
        "session_url": sessionURL
    });
    
    amplitude.getInstance().setUserProperties({
        "uxcam_url": userURL
    );
});

//tracking conditionally
if (purchased) {
    const sessionURL = await RNUxcam.urlForCurrentSession();
    amplitude.getInstance().logEvent("purchased", {
        "uxcam_session_url": sessionURL
    });
}
```
```go Flutter
//Set your amplitude instance
final Amplitude analytics = Amplitude.getInstance(instanceName: "project");

String sessionUrl = await FlutterUxcam.urlForCurrentSession();
String userUrl = await FlutterUxcam.urlForCurrentUser();
analytics.logEvent("uxcam", eventProperties: {
    "session_url": sessionURL
});

analytics.setUserProperties({
    "uxcam_url": userUrl
});

//tracking conditionally
if (purchased) {
    analytics.logEvent("purchased", eventProperties: {
        "uxcam_session_url": sessionURL
    });
}
```

### UXCam session URL in Amplitude

You can view your tracked event from Amplitude activity feed. 

![](https://files.readme.io/c808888-amplitude-dashboard.gif "amplitude-dashboard.gif")

### Log Amplitude metadata with UXCam

You can also bind Amplitude with UXCam. For example, you can log Amplitude session ID to UXCam as an Event to easily search for that specific session in UXCam Dashboard and watch the session replay. 

![](https://files.readme.io/4550f04-for_amplitude_2.png "for amplitude 2.png")

##### How To Do It:

```swift
//Log Amplitude session id to UXCam
UXCam.logEvent("amplitude", withProperties: [
    "session-id": Amplitude.instance()?.getSessionId()
])
```
```objectivec
//Log Amplitude session id to UXCam
[UXCam logEvent:@"amplitude" withProperties: @{
    @"session-id": [[Amplitude instance] getSessionId];
}];
```
```java Android
//Log Amplitude session id to UXCam
JSONObject eventProperties = new JSONObject();
eventProperties.put("session-id", Amplitude.getInstance().getSessionId());
UXCam.logEvent("amplitude", eventProperties);
```
```javascript React Native
//Log Amplitude session id to UXCam
RNUxcam.logEvent("amplitude", {
    "session-id": amplitude.getInstance().getSessionId()
});
```