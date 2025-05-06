---
title: Web SDK Integration Documentation
fullscreen: false
hidden: true
metadata:
  title: ''
  description: ''
---
## Overview

This documentation will guide you through the process of integrating the Web SDK into your website. The SDK allows for tracking user events, setting user identities, page views, and configuring user properties, with additional options for occlusion of sensitive data in URLs and query parameters.

## Installation

To integrate the Web SDK, include the following script in your HTML file before the closing \</head> tag:

```javascript
<script type="text/javascript" defer="">
(function(appKey, opts) {
    window.uxc = {
        __t: [],
        __ak: appKey,
        __o: opts,
        event: function(n, p) {
            this.__t.push(['event', n, p]);
        },
        setUserIdentity: function(i) {
            this.__t.push(['setUserIdentity', i]);
        },
        setUserProperty: function(k, v) {
            this.__t.push(['setUserProperty', k, v]);
        },
        setUserProperties: function(p) {
            this.__t.push(['setUserProperties', p]);
        },
    };
    var head = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = '//websdk-recording.uxcam.com/index.js';
    script.async = true;
    script.defer = true;
    script.id = 'uxcam-web-sdk';
    script.crossOrigin = 'anonymous';
    head.appendChild(script);
})('Your_App_Key', {});
</script>
```

***

<br />

> 📘 Please note
>
> You will need to integrate this script in all of your HTML elements in order to get the session recording of your entire website.

## What is Captured by Default

### Default Events

By default, the following events are captured:

* uxc\_input: Triggered when an input loses focus after it has been modified.
* uxc\_submit: Triggered when a form is submitted.
* uxc\_u\_turn: Triggered when a user goes back to the same page. The event is triggered on the page they went back to.\
  Example: If a user navigates Home > About Us > Home, it will be triggered on Home.
* uxc\_page\_refresh: Triggered when refreshing the page.

### Default Occlusion

Inputs will be occluded by default if they meet any of the following criteria:

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Input Types
      </th>
      <th>
        Input Names Containing
      </th>
      <th>
        Autocomplete Properties Containing
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        'password'
        'email'
        'tel'
        'hidden'
        'number'
        'hidden'
      </td>
      <td>
        'password'\
        'cc-'\
        'email'
      </td>
      <td>
        'cc-'\
        'address'\
        'phone'\
        'email'\
        'password'
      </td>
    </tr>
  </tbody>
</Table>

These inputs will be occluded with asterisks.

***

## Configuration Options

The SDK can be configured with optional parameters. The configuration object (`opts`) supports the following options:

### appVersion

Define the version of your application.

```javascript
{ appVersion: '1.0.0' }
```

### Occlusion

Enables occlusion of sensitive data in URLs and query parameters.

* Occluding Query Parameters\
  Query parameters to be occluded should be listed under queryParams.

```javascript
occlusion: {  
  queryParams: ['product', 'userId']  
}

// Example  
// Input: http://www.uxcam.com/query?product=shoes&userId=321  
// Output:http://www.uxcam.com/query?product=_occluded_&userId=_occluded
```

* Occluding URLs\
  A custom function can be used to occlude parts of the URL before the query parameters.

```javascript
occlusion: {  
  url: function(url) {  
    // Custom logic to modify the URL  
    return url.replace(/\/invite\/\w+/, '/invite/:inviteId');  
  }  
}  
// Example  
// Input: http://www.uxcam.com/invite/12345  
// Output: http://www.uxcam.com/invite/:inviteId
```

<br />

***

<br />

## Methods

The SDK provides methods to log events and set user properties.

### Logging an Event

**uxc.event(eventName, properties)**

Logs an event.

* eventName (string): The name of the event.
* properties (object, optional): A JSON object with event properties.

```javascript
uxc.event("your_amazing_event", { key: "value" });
```

### Setting User Identity

**uxc.setUserIdentity(userId)**

Sets the user identity.

* userId (string): The unique identifier for the user.

```javascript
uxc.setUserIdentity("user_12345");
```

### Setting a Single User Property

**uxc.setUserProperty(key, value)**

Sets a single user property.

* key (string): The property name.
* value (any JSON valid value): The property value.

```javascript
uxc.setUserProperty("age", 30);
```

### Setting Multiple User Properties

**uxc.setUserProperties(properties)**

Sets multiple user properties.

* properties (object): A JSON object where each key-value pair represents a user property.

```javascript
uxc.setUserProperties({ name: "John", age: 30 });
```

<br />

***

<br />

## Example Initialization with Configuration

Here is an example of initializing the SDK with an application version and occlusion settings:

```javascript
<script type="text/javascript" defer="">
(function(appKey, opts) {
    window.uxc = {
        __t: [],
        __ak: appKey,
        __o: opts,
        event: function(n, p) {
            this.__t.push(['event', n, p]);
        },
        setUserIdentity: function(i) {
            this.__t.push(['setUserIdentity', i]);
        },
        setUserProperty: function(k, v) {
            this.__t.push(['setUserProperty', k, v]);
        },
        setUserProperties: function(p) {
            this.__t.push(['setUserProperties', p]);
        },
    };
    var head = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = '//websdk-recording.uxcam.com/index.js';
    script.async = true;
    script.defer = true;
    script.id = 'uxcam-web-sdk';
    script.crossOrigin = 'anonymous';
    head.appendChild(script);
})('APP_KEY', {
    appVersion: '1.0.0',
    occlusion: {
        queryParams: ['product', 'userId'],
        url: function(url) {
            // Custom URL occlusion logic
            return url.replace(/\/invite\/\w+/, '/invite/:inviteId');
        }
    }
});
</script>
```

## Occlusion of HTML Elements

To occlude specific HTML elements, add the attribute `data-uxc="obfuscated"` to the elements you want to occlude.

```javascript
<div data-uxc="obfuscated">Sensitive Content</div>
```

## Conclusion

This concludes the documentation for integrating the Web SDK. If you have any questions or need further assistance, please contact [team@uxcam.com](mailto:team@uxcam.com).