---
title: Mixpanel
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
> 📘 Mixpanel provides event tracking and advanced reporting capabilities to gain insight to your data sets.

The following guide explains how to associate a UXCam session URL with Mixpanel event enabling you to see a step by step session replay for your every event.

## Sign up and Integrate UXCam

***

UXCam website will walk you through how to sign up and set up  UXCam. To create a Mixpanel account, visit Mixpanel's website.

For more information about 3rd party integration and best practices, visit our [3rd party integration guide](https://uxcam-documentation.readme.io/docs/other-3rd-party-integrations).

## Integrate with Mixpanel

***

1. [Set user identity](#set-user-identity)
2. [Track UXCam session](#track-uxcam-session)
3. [UXCam session URL in Mixpanel](#uxcam-session-url-in-mixpanel)
4. [Log Mixpanel metadata with UXCam](#log-mixpanel-metadata-with-uxcam)
5. [View your Mixpanel events from UXCam  
   ](#view-your-mixpanel-events-from-uxcam)  
   </br>

### Set User Identity

If your app requires registered user login, we highly encourage you to set user identity for UXCam and Mixpanel. By doing so, you can easily filter all the sessions for the user from UXCam user dashboard.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/cb89362-Screen_Shot_2020-09-10_at_10.48.03_AM.png",
        "Screen Shot 2020-09-10 at 10.48.03 AM.png",
        528
      ],
      "align": "center",
      "sizing": "smart"
    }
  ]
}
[/block]


#### How to do It:

```swift
//Set user identity for mixpanel
Mixpanel.mainInstance().identify(distinctId: "user@yourdomain.com")

//Set user identity for UXCam
UXCam.setUserIdentity("user@yourdomain.com")
```
```objectivec
//Set user identity for mixpanel
Mixpanel *mixpanel = [Mixpanel sharedInstance];
[mixpanel identify:@"user@yourdomain.com"];

//Set user identity for UXCam
[UXCam setUserIdentity:@"user@yourdomain.com"];
```
```java Android
//Set user identity for mixpanel
mixpanel.identify("user@yourdomain.com");

//Set user identity for UXCam
UXCam.setUserIdentity("user@yourdomain.com")
```

### Track UXCam session

You can attach a UXCam session URL to Mixpanel on UXCam session start or conditionally when a certain event occurs.

#### How to do It:

```swift
//tracking on session start
UXCam.start(withKey: "APP-KEY", buildIdentifier: nil) { (status) in
    if (status){
        if let sessionURL = UXCam.urlForCurrentSession() {
            Mixpanel.mainInstance().track(event: "UXCam: Session Recording link", properties: ["session_url": sessionURL])
        }
        
        if let userURL = UXCam.urlForCurrentUser() {
            Mixpanel.mainInstance().people.set(properties: ["uxcam_user_url": userURL])
        }
    }
}

//tracking conditionally
if (purchased){
    if let sessionURL = UXCam.urlForCurrentSession() {
        Mixpanel.mainInstance().track(event: "purchased", properties: ["uxcam_session_url": sessionURL])
    }
}
```
```objectivec
//tracking on session start
[UXCam startWithKey:@"APP-KEY" buildIdentifier:nil completionBlock:^(BOOL started) {
    if (started){
        NSString* sessionURL = [UXCam urlForCurrentSession];
        NSString* userURL = [UXCam urlForCurrentUser];
        
        Mixpanel *mixpanel = [Mixpanel sharedInstance];
        [mixpanel track:@"UXCam: Session Recording link" properties:@{ @"session_url": sessionURL }];
        
        [mixpanel.people set:@{@"uxcam_user_url": userURL}];
    }
}];

//tracking conditionally
if (purchased) {
    NSString* sessionURL = [UXCam urlForCurrentSession];
    Mixpanel *mixpanel = [Mixpanel sharedInstance];
    [mixpanel track:@"purchased" properties:@{ @"uxcam_session_url": sessionURL }];
}
```
```java Android
// Inside onCreate method of all activity that is an entry point to your app add
UXCam.startWithKey("APP-KEY");
UXCam.addVerificationListener(new UXCam.OnVerificationListener() {
    @Override
    public void onVerificationSuccess() {
        //Tag Mixpanel events with UXCam recording URLS. Example:
        JSONObject eventProperties = new JSONObject();
        try {
            eventProperties.put("uxcam_session_url", UXCam.urlForCurrentSession());
        } catch (JSONException exception) {
        }
        mixpanel.track("UXCam: Session Recording link", eventProperties);
        //Tag Mixpanel profile with UXCam user URLS. Example:
        mixpanel.getPeople().set("uxcam_user_url", UXCam.urlForCurrentUser());
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
    mixpanel.track("purchased", eventProperties);
}
```

### UXCam session URL in Mixpanel

You can view your tracked event from Mixpanel activity feed.

![](https://files.readme.io/5b2e0f0-Screen_Shot_2020-09-04_at_12.17.18_PM.png "Screen Shot 2020-09-04 at 12.17.18 PM.png")

### Log Mixpanel metadata with UXCam

You can also bind Mixpanel with UXCam. For example, you can log Mixpanel user identity to UXCam which can identify your registered user for the session.

#### How to do it:

```swift
//Log mixpanel user id to UXCam
let user = Mixpanel.mainInstance().distinctId
UXCam.logEvent("mixpanel", withProperties: ["mixpanel-user-id": user])
```
```objectivec
//Log mixpanel user id to UXCam
NSString* userId = [mixpanel distinctId];
[UXCam logEvent:@"mixpanel" withProperties: @{@"mixpanel-user-id": userId}];
```
```java Android
//Log mixpanel user id to UXCam
String userId = mixpanel.distinctId;
JSONObject eventProperties = new JSONObject();
eventProperties.put("mixpanel-user-id", userId);
UXCam.logEvent("mixpanel", eventProperties);
```

### View your Mixpanel events from UXCam

You can filter events related to Mixpanel from your events dashboard.