---
title: Intercom
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
If you are using Intercom to communicate with your users and help them solve their problems, you can integrate it with UXCam to get a **better understanding of the reported issues**, see which screens your users visited or which events were triggered before contacting you.

### Benefits:

- **Connect your users** across both platforms to easily find and reproduce your users' sessions, understand and analyze their journey. 
- Provide **faster and better solutions** to your users' issues by watching their session recordings and understanding when and why the issue happened.
- **Gain more insights and customize conversations** with your users, thanks to UXCam's session replay and event analytics you can better understand their actions before replying. 

## How UXCam-Intercom integration works:

***

### 1. Sync your Intercom and UXCam user IDs:

Attach your Intercom user’s IDs to UXCam by sending them as user properties. That way you can easily find in UXCam Dashboard all the sessions for the user’s that connected with you on Intercom.

> 📘 Example
> 
> The user Peter reported an issue in your app. Understanding what happened and replicating the user’s steps can be hard. After attaching your Intercom user ID "Peter" as "userid" in the UXCam SDK, you can easily filter for Peter’s sessions on your UXCam dashboard and watch the video recordings to gather more insights and better understand the issue. You will be able to address these problems faster and more accurately while increasing your customer satisfaction.

> 📘 
> 
> Please keep in mind that if you're sending any PII data you will need to sign a DPA agreement with us.

### How to do it:

If you are sending custom user properties such as User ID to Intercom (Follow this [guide](https://www.intercom.com/help/en/articles/179-send-custom-user-attributes-to-intercom) to do it) or you’re requiring your users to register before sending a message, you can send that data as a user property to UXCam.

```swift
//Register with userId for Intercom
//Use withEmail instead of withUserId for email registration
Intercom.registerUser(withUserId: "your-user-id")

//Set user identity for UXCam
UXCam.setUserIdentity("your-user-id")
```
```objectivec
//Register with userId or email for Intercom
[Intercom registerUserWithUserId:@"your-user-id"];
[Intercom registerUserWithEmail:@"user@yourdomain.com"];

//Set user identity for UXCam
[UXCam setUserIdentity:@"your-user-id"];
```
```java Android
//Register with userId or email for Intercom
//Use withEmail("user@yourdomain.com") for email registration
Registration registration = Registration.create().withUserId("user-id");
Intercom.client().registerIdentifiedUser(registration);

//Set user identity for UXCam
UXCam.setUserIdentity("user-id")
```
```javascript Cordova
//Register with userId or email for Intercom
//Use email instead of userId for email registration
intercom.registerIdentifiedUser({userId: "user-id"});

//Set user identity for UXCam
UXCam.setUserIdentity("user-id")
```

### 2. Sync your Intercom events and UXCam session replays

**Get to know your users and personalize your conversations based on your user’s behavior.**

You can send multiple events to Intercom to get more insights about your users, create segments, and send targeted messages. By attaching the UXCam session and user URL to intercom Events you can further connect UXCam with Intercom to have more context and analyze your user’s experience. 

That way you can **customize your conversations based on your user’s behavior** and help them through the onboarding process or with achieving some milestones within your app. 

> 📘 Example
> 
> Your support team is chatting with a user who is asking how to complete a purchase - they can see in the Intercom dashboard that the user triggered the event "Add to cart", but he indeed didn't trigger the event "Purchase completed". On the Intercom dashboard they can just click on the UXCam user or session URL for the "Add to cart" event to directly watch the session and understand how that user interacted with the app, analyze his journey and pinpoint the reasons why he may not have completed the process, to easily address all his questions and guide him in the right direction.

### How to do it:

To send events to Intercom you can just follow this [Guide](https://www.intercom.com/help/en/articles/175-set-up-event-tracking-in-intercom). Additionally to the Event name, you can send any Metadata. Therefore, you can send UXCam sessions’ and user’s URLs attached to the event.

We **recommend sending the same events to UXCam** to have more consistency in your data. 

#### **In UXCam**

Attach a UXCam session URL to Intercom right after initialization or conditionally when a certain event occurs.

```swift
//Logging on session start
UXCam.start(withKey: "APP-KEY", buildIdentifier: nil) { (status) in
    if (status){
        //Log event
        if let sessionURL = UXCam.urlForCurrentSession() {
            Intercom.logEvent(withName: "UXCam: Session Recording link", metaData: ["session_url": sessionURL])
        }
        
        //Update user attributes
        if let userURL = UXCam.urlForCurrentUser() {
            let userAttributes = ICMUserAttributes()
            userAttributes.customAttributes = ["uxcam_user_url": userURL]
            Intercom.updateUser(userAttributes)
        }
    }
}

//Logging conditionally
if (purchased){
    if let sessionURL = UXCam.urlForCurrentSession() {
        Intercom.logEvent(withName: "purchased", metaData: ["uxcam_session_url": sessionURL])
    }
}
```
```objectivec
//Logging on session start
[UXCam startWithKey:@"APP-KEY" buildIdentifier:nil completionBlock:^(BOOL started) {
    if (started){
        NSString* sessionURL = [UXCam urlForCurrentSession];
        NSString* userURL = [UXCam urlForCurrentUser];
        
        //Log event
        [Intercom logEventWithName:@"UXCam: Session Recording link" metaData: @{
            @"session_url": sessionURL
        }];
        
        //Update user attributes
        ICMUserAttributes *userAttributes = [ICMUserAttributes new];
        userAttributes.customAttributes = @{@"uxcam_user_url" : userURL};
        [Intercom updateUser:userAttributes];
    }
}];

//Logging conditionally
if (purchased) {
    NSString* sessionURL = [UXCam urlForCurrentSession];
    [Intercom logEventWithName:@"purchased" metaData: @{
        @"uxcam_session_url": sessionURL
    }];
}
```
```java Android
// Inside onCreate method of all activity that is an entry point to your app add
UXCam.startWithKey("APP-KEY");
UXCam.addVerificationListener(new UXCam.OnVerificationListener() {
    @Override
    public void onVerificationSuccess() {
        //Tag Intercom events with UXCam recording URLS. Example:
        JSONObject eventProperties = new JSONObject();
        try {
            eventProperties.put("uxcam_session_url", UXCam.urlForCurrentSession());
        } catch (JSONException exception) {
        }
        Intercom.client().logEvent("UXCam: Session Recording link", eventProperties);
        
        //Update user attributes with UXCam user URLS. Example:
        UserAttributes userAttributes = new UserAttributes.Builder()
        .withCustomAttribute("uxcam_user_url", UXCam.urlForCurrentUser())
        .build();
        Intercom.client().updateUser(userAttributes);
    }
    @Override
    public void onVerificationFailed(String errorMessage) { }
});

//Logging conditionally
if (purchased) {
    JSONObject eventProperties = new JSONObject();
    try {
        eventProperties.put("uxcam_session_url", UXCam.urlForCurrentSession());
    } catch (JSONException exception) { }
    Intercom.client().logEvent("purchased", eventProperties);
}
```
```javascript Cordova
//Logging on session start
UXCam.startWithKey("APP-KEY", ()=>{
    //Log event
    intercom.logEvent("UXCam: Session Recording link", {
        uxcam_session_url: UXCam.urlForCurrentSession()
    });
    
    //Update user attributes
    intercom.updateUser({
        custom_attributes: {
            uxcam_user_url : UXCam.urlForCurrentUser()
        }
    });
});

//Logging conditionally
if (purchased) {
    intercom.logEvent("purchased", {
        uxcam_session_url: UXCam.urlForCurrentSession()
    });
}
```

You can **view the attached events** in your Intercom user's detail page:

![](https://files.readme.io/98623fc-For_intercom_article.png "For intercom article.png")

### 3. Sync your Intercom and UXCam events

You can send the same Intercom events to UXCam, to not only have more consistency in your data but also quantitatively analyze them with our **Events Analytics feature**, or have more qualitative data by easily watching the recordings where those events happened and understand what led your users to make some decisions while interacting with your app.

You can also build funnels including relevant Events and **analyze the drop-offs or user’s pain points** within your app. Next, you can easily **find these users on Intercom** and contact them to help them through the process, improving their experience. 

You can read more about logging Events to UXCam [here](https://uxcam-documentation.readme.io/docs/send-events).