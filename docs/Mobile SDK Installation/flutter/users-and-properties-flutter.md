---
title: Custom Users and Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    UXCam allows you to identify users with a unique User ID and send up to 100
    additional user properties for deeper insights into user behavior, while
    recommending against using Personally Identifiable Information (PII) unless
    a Data Processing Agreement is signed.
  robots: index
next:
  description: ''
---
By default, UXCam generates a random alias name to identify your users based on the Install ID. We also show you other properties about the user such as location, device used, network type, and app version. However, you can send up to 100 additional user properties with our API.

**Sending user properties allows you to:**

Gain deeper insights into user behaviour, enabling you to create segments and compare behaviour across different audiences.

* Easily identify users using a unique User ID, which can help you diagnose user issues and offer better support.
* Track users across multiple devices, ensuring a unified view of their journey.
* Have a better understanding of your users’ actions, create segments, and discover how behavior changes between different audiences.

We recommend avoiding the use of Personally Identifiable Information (PII), such as email addresses or phone numbers. Instead, use a unique User ID to identify your users in UXCam. If you need to send PII, you must sign a Data Processing Agreement (DPA) with us. Please contact [team@uxcam.com](mailto:team@uxcam.com) for further discussion.

<br />

## Set Custom User Identity

You can replace the default alias with your own user ID using this method:

```dart Flutter
import 'package:flutter_uxcam/flutter_uxcam.dart';

FlutterUxcam.setUserIdentity(userIdentity);
```

**API Parameter:**

`userIdentity`: The custom identifier for the user.

<br />

## Sending Custom User Information

Additional custom user properties help you group sessions, users, or events based on specific attributes such as roles, company names, subscription types, and more. You can use this data to refine your analysis and tailor user experiences effectively.

**Example User Properties:**

* Role
* Company Name
* Acquisition Source
* Subscription Type
* Loyalty Membership
* NPS Score or Rating

```dart Flutter
import 'package:flutter_uxcam/flutter_uxcam.dart';

// Set user property
FlutterUxcam.setUserProperty(key, value);
    
//Example
FlutterUxcam.setUserProperty("role", "your-role");
FlutterUxcam.setUserProperty("subscription_type", "premium");
FlutterUxcam.setUserProperty("company_name", "your-company");
```

**API Parameters:**

`propertyName`: The name of the property to attach to the user.\
`value`: A value to associate with the property. String or Number are accepted for value.

> 🚧 Note: User IDs and properties are case sensitive. Please ensure consistent naming conventions when using them.
