---
title: Segment
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
If you are using Segment to track your events and collect useful information, you can integrate it with UXCam to **get the full context of your users' interaction**. To connect Segment with UXCam, you can use the middleware integration which **translates Segment's tracking methods into UXCam's methods** to automatically send screen names, events with properties, and user properties from **Segment to UXCam**. 

> 🚧
>
> Please note that the **Segment\<> UXCam** integration is only possible via middleware, which has to be added directly into your code. The tracking methods used will depend on your needs and your middleware implementation.

> 📘
>
> If you haven’t already, please integrate UXCam following the steps described for [iOS](https://uxcam-documentation.readme.io/docs/ios-beta) or [Android](https://uxcam-documentation.readme.io/docs/android)
>
> To read more about Segment's integration process, please visit [this page ](https://segment.com/docs/)

## <br/>

## Supported Segment Calls and Equivalents

Here you can view a list of Segment's available calls and the UXCam supported methods you can map it to:

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Segment API
      </th>

      <th>
        UXCam API
      </th>

      <th>
        Usability
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        [Identify](https://segment.com/docs/connections/spec/identify/)
      </td>

      <td>
        [setUserProperties](https://uxcam-documentation.readme.io/docs/send-user-properties)
      </td>

      <td>
        Set name and additional properties to your users.
      </td>
    </tr>

    <tr>
      <td>
        [Track](https://segment.com/docs/connections/spec/track/)
      </td>

      <td>
        [logEvent](https://uxcam-documentation.readme.io/docs/send-events)
      </td>

      <td>
        Follow actions that are performed by your users and its properties.
      </td>
    </tr>

    <tr>
      <td>
        [Screen](https://segment.com/docs/connections/spec/screen/)
      </td>

      <td>
        [tagScreenName](https://uxcam-documentation.readme.io/docs/tag-of-screens)
      </td>

      <td>
        Lets you name the screen the user has visited.
      </td>
    </tr>

    <tr>
      <td>
        [Alias](https://segment.com/docs/connections/spec/alias/)
      </td>

      <td>
        [setUserIdentity](https://uxcam-documentation.readme.io/docs/send-user-properties)
      </td>

      <td>
        Assign a new identity to a previous user.\ <sub>*(We recommend using the identify call as it already is able to send user identity)* </sub>
      </td>
    </tr>
  </tbody>
</Table>

<br />

***

# Android & iOS

## Initialization Steps

And after initializing the UXCam SDK, you can create Segment middleware and initialize Segment, inside the middleware code you can add the cases you need to send to UXCam:\
 <sub>(i.e only track events or track + screens)</sub>

```swift
let segmentConfiguration = AnalyticsConfiguration(writeKey: SEGMENT_API_KEY)
// Make other configuration changes
let uxcamMiddleware = BlockMiddleware(block: { (context, next) in

switch (context.eventType) {
// Add your cases here
  }
default: break

}
next(context)

})

segmentConfiguration.sourceMiddleware = [uxcamMiddleware]
Analytics.setup(with: segmentConfiguration)
```
```objectivec
SEGAnalyticsConfiguration *segmentConfiguration = [SEGAnalyticsConfiguration configurationWithWriteKey: SEGMENT_API_KEY
// Make other configuration changes
SEGBlockMiddleware *uxcamMiddleware = [[SEGBlockMiddleware alloc] initWithBlock:^(SEGContext * _Nonnull context, SEGMiddlewareNext  _Nonnull next) {

switch (context.eventType) {

    // Add your cases here
    
default: break;
}

next(context);

}];
segmentConfiguration.sourceMiddleware = @[uxcamMiddleware];
[SEGAnalytics setupWithConfiguration:segmentConfiguration];
```
```java
new Analytics.Builder(getApplicationContext(), SEGMENT_API_KEY)
      .recordScreenViews()
      .useSourceMiddleware(new Middleware() {
          @Override
          public void intercept(Chain chain) {
              BasePayload payload = chain.payload();
              switch (payload.type()) {

                   // Add your cases here
                    
              }
              chain.proceed(payload);
         }
     }).build();
```
```kotlin
Analytics.Builder(applicationContext, SEGMENT_API_KEY)
  .recordScreenViews() 
  .useSourceMiddleware { chain ->
     val payload = chain.payload()
     when (payload.type()) {
             
    			// Add your cases here
    }
     chain.proceed(payload)
 }.build()
```

***

### Send Events to UXCam

Add a case .track to your middleware:

```swift
case .track:

   if let track = context.payload as? TrackPayload {

       var properties = [String: String]()
       // Stringify track properties
       track.properties?.forEach({ (key: AnyHashable, value: Any) in

       properties["\(key)"] = "\(value)"

       })

     let check = track.event
     if check != "button to ignore" {
     UXCam.logEvent(track.event, withProperties: properties)

    }}
```
```objectivec
case SEGEventTypeTrack:

if ([context.payload isKindOfClass:[SEGTrackPayload class]]) {

SEGTrackPayload *track = (SEGTrackPayload *)context.payload;
// Converting property from Segment payload to match UXCam payload
// Stringifying Key and Value of properties
NSDictionary *oldProperties = track.properties;
NSMutableDictionary *newProperties = [NSMutableDictionary dictionary];
if (oldProperties != nil && oldProperties.count > 0) {

[oldProperties enumerateKeysAndObjectsUsingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {

NSString *keyString = [NSString stringWithFormat:@"%@", key];
NSString *value = [NSString stringWithFormat:@"%@", obj];
newProperties[keyString] = value;

}];

}
[UXCam logEvent:track.event withProperties:newProperties];

}
break;
```
```java
case track:
      Map<String, Object> params = new HashMap<>();

        String key = payload.getString("event");
        ValueMap map = payload.getValueMap("properties");
            if (map != null) {
               for (Map.Entry<String, Object> entry : map.entrySet()) {
                   params.put(entry.getKey(), entry.getValue());
               }
            }

         UXCam.logEvent(key, params);
         break;
```
```kotlin
BasePayload.Type.track -> {
   val params: MutableMap<String, Any> = HashMap()
   val key = payload.getString("event")

   payload.getValueMap("properties")?.let {
      for ((paramKey, paramValue) in it) {
        params[paramKey] = paramValue
      }
   }

   UXCam.logEvent(key, params)
}
```

***

### Tag Screen Name

Add a case .screen to your middleware:

```swift
case .screen:

        if let screen = context.payload as? ScreenPayload {

          var properties = [String: String]()
          // Stringify track properties
          screen.properties?.forEach({ (key: AnyHashable, value: Any) in

           properties["\(key)"] = "\(value)"

        })

          UXCam.tagScreenName(screen.name)

        }
```
```objectivec
```
```java
case screen:
   String screenName = payload.getString("name");
   UXCam.tagScreenName(screenName);
   Break;
```
```kotlin
BasePayload.Type.screen -> {
     val screenName = payload.getString("name")
     UXCam.tagScreenName(screenName)
}
```

***

### Set User Identity

```swift
case .identify:

        if let user = context.payload as? IdentifyPayload {

          var userProperties = [String: String]()
          // Stringify track properties
          user.traits?.forEach({ (key: AnyHashable, value: Any) in

           userProperties["\(key)"] = "\(value)"

        })

          UXCam.setUserIdentity(user.userId)

        }
```
```objectivec
```
```java
case identify:
   String userId = payload.userId();
    if (userId != null) {
        UXCam.setUserIdentity(userId);
    }

   Object payloadTraits = payload.get("traits");
    if (payloadTraits instanceof Map) {
        Map<Object, Object> traits = (Map<Object, Object>) payloadTraits;
         for (Map.Entry<Object, Object> entry : traits.entrySet()) {                                      UXCam.setUserProperty(entry.getKey().toString(), entry.getValue().toString());
         }
    }
break;
```
```kotlin
BasePayload.Type.identify -> {
             payload.userId()?.let {
                 UXCam.setUserIdentity(it)
             }

             payload["traits"]?.let {
                 val traits: Map<*, *>? = it as? Map<*, *>
                 traits?.let {
                     for ((key, value) in traits) {
                         UXCam.setUserProperty(key.toString(), value.toString())
                     }
                 }
            }
         }
```

***

# React Native

## Initialization Steps

1. Create a new typescript plugin file named “uxcam\_segment\_plugin.ts” with the below contents:

```typescript React Native
// UXCamSegmentPlugin.ts
 
import {
 EventPlugin,
 IdentifyEventType,
 TrackEventType,
 ScreenEventType,
 PluginType,
 JsonValue,
 } from '@segment/analytics-react-native';
 
 import RNUxcam from 'react-native-ux-cam';
  export class UXCamSegmentPlugin extends EventPlugin {
 
   type = PluginType.before;
  
   identify(event: IdentifyEventType) {
     if (event.userId) { RNUxcam.setUserIdentity(event.userId); }
     if (event.traits) {
       Object.entries(event.traits).forEach(
         ([key, value]) => this.setUserProperty(key, value)
     );
      
     }
     return event;
   }
 
   setUserProperty(key: string, value: JsonValue) {
     // Only string and number are supported for UXCam setUserProperty
       if (typeof value === 'string' || typeof value === 'number') {
         RNUxcam.setUserProperty(key, value);
       }
   }
    track(event: TrackEventType) {
     if (event.properties) {
       RNUxcam.logEvent(event.event, event.properties);
     }
     return event;
   }
    screen(event: ScreenEventType) {
     RNUxcam.tagScreenName(event.name);
     return event;
   }
 }
```

2. Add the created plugin as segment plugin in the starting point of the app, mainly “App.js”

```typescript React Native
import { UXCamSegmentPlugin } from './src/helpers/segment_plugin';
segmentClient.add({ plugin: new UXCamSegmentPlugin() });
```

3. Now simply start using these segment methods as normal and those calls will automatically call respective UXCam methods to store data in the UXCam Dashboard:

```typescript React Native
export const segmentClient = createClient({
   writeKey: "SEGMENT_KEY",
   trackAppLifecycleEvents: true,
   //additional config options
  });

// below example will call `logEvent` method on UXCam
segmentClient.track(“button-click”, {“button”: true})

segmentClient.screen(“screen1”) // call `tagScreenName` method on UXCam
```

> 👍
>
> **This will complete the integration process.\&#xA;**&#x54;hese tracking methods you're sending to Segment, will be logged on UXCam’s Dashboard as events, events properties, user properties, and tracked screens.

***

# FAQs

* **Is it possible to send specific events to UXCam instead of all of them?**

     Yes, a simple conditional statement to filter events will work. Our middleware is an example and **implementation depends on developers and their requirements**. Filter, modification and any other logics can be added to the middleware as in any other block of code.

* **Can I send multiple methods at the same time to UXCam?**

     Definitely, you can send all available tracking methods from Segment to UXCam at the same time, simply adding each case to the middleware. Plase, keep in mind that the order in which you set the cases can affect the order in which they appear in UXCam (i.e events being sent before a screen gets tagged can show as the event being triggered in a previous screen)

* **Can I send data from UXCam to Segment?**

     This middleware **only allows** to send data **from** Segment **to** UXCam, it is not both ways.

* **Which events can be sent to UXCam from Segment?**

     See the Supported Segment Calls [above](#supported-segment-calls-and-equivalents)

* **Is there anything I should do after adding the middleware?**\
     After adding the middleware, events and properties you've selected to send to UXCam will start appearing in your Dashboard on the next sessions without having to perform any additional actions. If you want to customize which events, properties or how diverse tracking methods are sent, you'd have to do so on your app's code. 

* **How does it looks like from UXCam side (Dashboard)?** 

     Events and properties you send from Segment will appear as a regular event/property or screen on UXCam as if you've sent them using our APIs

* **If I change or edit one event in Segment, will it be automatically updated in UXCam?** 

     Yes, edited properties or events will be updated on the following session after you've made these changes. In the case of events and properties, old ones you've eliminated will remain visible in previous sessions (or users) properties.

* **Do I need to review this integration for every release?** 

     If you have made significant changes that affect the events, screens or the way users are defined in your app or in Segment, it's advisable to review the middleware and verify everything is being sent properly. If no major changes have been made, the middleware can keep tracking the methods you've already added.

* **Do you have a limit on the number of events that I can send?** 

     You're able to send any amount of events to the middleware, however please keep in mind that UXCam will only record up to the maximum event count your account has in your subscription. 

* **Do you have a limit on the number of event properties I can send?** 

     There can be up to 20 properties per event.

* **Do you have a limit on the number of user properties I can send?**

     You can send up to 100 properties for each of your users, with each property having a maximum length of 1024 characters.

* **What happens when I hit the limits?**

     If you exceed the amount of events in your subscription details, no further events will be recorded until your next cycle gets reset.\
     If you exceed the amount of event or user properties being sent on an event, the extra properties will not be shown and you'll see a message indicating there's an excess in your event or user properties.
