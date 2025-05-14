---
title: Custom Events and Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    Events are essential for tracking user interactions in your app, providing
    insights for data-driven decisions to enhance user experience, and can be
    logged with properties for additional context using UXCam's methods.
  robots: index
next:
  description: ''
---
Events are powerful tools for tracking user interactions within your application. By sending events, you can gain deeper insights into how users are interacting with your product and make data-driven decisions to improve the user experience.

<br />

> 📘 Note:
>
> UXCam automatically detects[ UI Freezes ](https://help.uxcam.com/hc/en-us/articles/360045884471)and[ Rage taps](https://help.uxcam.com/hc/en-us/articles/360036136992) and logs them as an event; for example, Rage taps are registered as "Rage Tap" events.

## How to Send Events

To send an event, use the following method in your code:

```coffeescript Xamarin
void UXCam.LogEvent(string eventName)
```

Replace `"EventName"` with a meaningful name that describes the action being tracked, such as `"ButtonClicked" `or` "UserLoggedIn"`. Naming your events consistently will make your analytics easier to understand.

## Sending Events with Properties

Events can also have properties associated with them to provide additional context. For example, if you have an event called "`Purchase`", you might include properties like the product ID, product category, or price.

To send an event with properties, use the following method:

```coffeescript Xamarin
void UXCam.LogEvent(string eventName, JSONObject properties) //Android
void UXCam.LogEvent(string eventName, NSObject properties) //iOS
```
```coffeescript MAUI
UXCam.LogEvent("Event Name", new Dictionary<string, object> { { "stringValue", "value" }, {"numeric", 1} });
```

This approach gives you a richer dataset and helps in better segmenting and understanding your users' behaviour.

### Important Considerations

**Properties Limit**: Each event can include up to 20 properties. If you exceed this limit, any extra properties will not be shown, and you'll receive a message indicating that there's an excess of properties.

**Case Sensitivity**: Event names and property keys are case sensitive. This means "purchase" and "Purchase" will be treated as two different events, so be consistent with naming to avoid confusion.

### Best Practices

**Be Consistent**: Use consistent naming conventions for event names and properties. This makes it easier to search and analyze your events in UXCam.

**Keep Properties Relevant:** Only add properties that provide meaningful context. Overloading events with unnecessary properties can make your analytics harder to interpret.

**Test Your Implementation**: Before rolling out your analytics to production, thoroughly test your events and properties to ensure they are correctly logged and appear as expected.

By leveraging events and properties effectively, you can unlock valuable insights into how users interact with your app, leading to better product decisions and enhanced user experiences.