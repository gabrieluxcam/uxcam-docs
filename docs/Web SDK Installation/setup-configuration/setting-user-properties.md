---
title: Setting User Properties
deprecated: false
hidden: false
metadata:
  robots: index
---
To personalise analytics and enable richer user insights, the SDK allows you to set a user’s identity and assign custom properties. These methods help track user behaviour more meaningfully by associating events with specific users and attributes.

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