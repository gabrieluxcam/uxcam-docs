---
title: Send Events and Properties
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
You can send events and events' properties to UXCam to gather more insights about your users' behavior. It will help you to:  

* Create more granular funnels thanks to the combination of Events and Screens.
* Follow your user journey closely by analyzing all the relevant steps.
* Easily identify errors in your user journey or conversion process.
* Track each step of your registration process to easily identify drop-offs.
* Measure and analyze the adoption of new features.

E.g You have an app to book movie tickets. You’re sending the Event ‘ticket\_booked,’  you can then attach several properties to the event to provide more context to that event, such as: ‘Movie\_Category’, ‘Movie\_Name’, ‘Movie\_Time’, ‘Quantity’, ‘Price’, etc. 

Need some inspiration? [Check this article to find some Events ideas →](https://help.uxcam.com/hc/en-us/articles/360043593691)

All the events will be attached to the respective sessions and users; therefore, you can easily use filters to find sessions or users that triggered a specific event. What’s more, you can analyze your events with Event Analytics or use them to build funnels.

> 📘
>
> Note: UXCam automatically detects[ UI Freezes ](https://help.uxcam.com/hc/en-us/articles/360045884471)and[ Rage taps](https://help.uxcam.com/hc/en-us/articles/360036136992) and logs them as an event; for example, Rage taps are registered as "Rage Tap" events.

### Log Events

Log an event into the timeline along with custom properties

```swift iOS
logEvent(_ eventName: String)
logEvent(_ eventName: String, withProperties properties: [String: Any]?)
    
//Example
UXCam.logEvent("purchased")
UXCam.logEvent("purchased",withProperties: [
    "size": 7,
    "brand": "Nike"
])
```
```java Android
logEvent(String eventName)
logEvent(String eventName, Map properties)
logEvent(String eventName, JSONObject properties)

//Example
UXCam.logEvent("purchased");
//With properties
final Map<String, String> properties = new HashMap<String!, Any!>();
properties.put("size", "7");
properties.put("brand", "Nike");
UXCam.logEvent("purchased", properties);
```
```javascript React Native
RNUxcam.logEvent: (eventName: string, properties?: any) => void

//Example
RNUxcam.logEvent("purchased");
RNUxcam.logEvent("purchased", {
  "size": 7,
  "brand": "Nike"
});
```
```javascript Flutter
logEvent(String eventName)
logEventWithProperties(String eventName, Map<String, dynamic> properties)

//Example
FlutterUxcam.logEvent("purchased");
FlutterUxcam.logEventWithProperties("purchased", {
    "size": 7,
    "brand": "Nike"
});
```
```csharp Xamarin
void UXCam.LogEvent(string eventName)
void UXCam.LogEvent(string eventName, JSONObject properties) //Android
void UXCam.LogEvent(string eventName, NSObject properties) //iOS
```
```javascript Cordova
UXCam.logEvent: (eventName: string) => void
UXCam.logEventWithProperties: (eventName: string, properties: any) => void

//Example
UXCam.logEvent("purchased")
UXCam.logEventWithProperties("purchased", {
    "size": 7,
    "brand": "Nike"
});
```

> 📘
>
> The API parameters are:\
> **eventName**: A tag to attach to the session recording at the current time\
> **properties**: (Optional) A Dictionary of properties to associate with this event. Only number and string property types are supported.

> 📘 Tips on Sending Dates as Event Properties:
>
> In terms of the date formats, there are many options that will work, but generally we would recommend to use the UTC time format (‘YYYY-MM-DDTHH:MM:SSZ’) or either of the following options:\
> ‘YYYY-MM-DD HH-MM-SS’\
> ‘YYYY/MM/DD HH-MM-SS’\
> ‘YYYY-MM-DD’\
> ‘YYYY/MM/DD’\
> ‘YYYY’.\
> The separators you use don’t matter as long as they are consistent. For instance if you compare ‘YYYY/MM’  to ‘YYYY-MM’ you will get sensible results.\
> Alternatively, you can just attach the Unix time (seconds since 1970), and then use simple integer comparison [https://www.utctime.net/utc-timestamp](https://www.utctime.net/utc-timestamp) which will work already.

### Things to Consider

* If you pass more than the limit 20 properties for a single event, instead, an event called \_UXCam\_Overload will appear mentioning the limit has been exceeded for that event. 

<Image align="center" src="https://files.readme.io/79bc10d8d7478cc438efc2c160b7dbed11ee24be159af25d10ea7b01ec0efd05-image_15.png" />
