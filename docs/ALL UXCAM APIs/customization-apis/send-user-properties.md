---
title: Send User Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: send-events
      title: Send Events and Properties
---
By default, UXCam generates a random alias name to identify your users based on the Install ID. We also show you other properties about the user such as location, device used, network type, and app version. However, you can send up to 100 additional user properties with our API. 

**Sending additional user properties will help you:**
* Have a better understanding of your users’ actions, create segments, and discover how behavior changes between different audiences.
* Easily identify your users with a unique User ID to pinpoint their issues and provide better support.
* Identify your users across multiple devices.

We recommend not sending **PII data**, such as email address or phone number, and instead, send your own and unique User ID to UXCam. However, if you do need to send PII data, you will need to sign a DPA agreement with us. Please contact team@uxcam.com to discuss it.

### Set user Identity
You can replace the default alias with your own user ID using this method:
[block:code]
{
  "codes": [
    {
      "code": "UXCam.setUserIdentity(_ userIdentity: String)",
      "language": "swift",
      "name": "iOS"
    },
    {
      "code": "UXCam.setUserIdentity(String userIdentity);",
      "language": "java",
      "name": "Android"
    },
    {
      "code": "RNUxcam.setUserIdentity: (userIdentity: string) => void",
      "language": "javascript",
      "name": "React Native"
    },
    {
      "code": "void setUserIdentity(String userIdentity)",
      "language": "javascript",
      "name": "Flutter"
    },
    {
      "code": "void UXCam.SetUserIdentity(string userIdentity)",
      "language": "csharp",
      "name": "Xamarin"
    },
    {
      "code": "UXCam.setUserIdentity: (userIdentity: string) => void",
      "language": "javascript",
      "name": "Cordova"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "info",
  "body": "The API parameters are:\n**userIdentity**: The new name for the user."
}
[/block]
### Send Additional User Information

You can send additional user properties to gather more insights or to easily filter or group sessions, users, or events by specific user properties.
[block:callout]
{
  "type": "info",
  "body": "E.g.  Role, Company name, Acquisition source, Subscription type, Loyalty membership, NPS score or rating, etc."
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "setUserProperty(_ propertyName: String, value: Any)\n    \n//Example\nUXCam.setUserProperty(\"role\", value: \"your-role\")\nUXCam.setUserProperty(\"subscription_type\", value: \"premium\")\nUXCam.setUserProperty(\"company_name\", value: \"your-company\")",
      "language": "swift",
      "name": "iOS"
    },
    {
      "code": "setUserProperty(String propertyName, String value)\n    \n//Example\nUXCam.setUserProperty(\"role\",\"your-role\")\nUXCam.setUserProperty(\"subscription_type\",\"premium\")\nUXCam.setUserProperty(\"company_name\",\"your-company\")",
      "language": "java",
      "name": "Android"
    },
    {
      "code": "RNUxcam.setUserProperty: (propertyName: string, value: string | number) => void\n    \n//Example\nRNUxcam.setUserProperty(\"role\",\"your-role\")\nRNUxcam.setUserProperty(\"subscription_type\",\"premium\")\nRNUxcam.setUserProperty(\"company_name\",\"your-company\")",
      "language": "javascript",
      "name": "React Native"
    },
    {
      "code": "void setUserProperty(String key, String value)\n    \n//Example\nFlutterUxcam.setUserProperty(\"role\",\"your-role\")\nFlutterUxcam.setUserProperty(\"subscription_type\",\"premium\")\nFlutterUxcam.setUserProperty(\"company_name\",\"your-company\")",
      "language": "javascript",
      "name": "Flutter"
    },
    {
      "code": "void UXCam.SetUserProperty(string propertyName, string value)\n    \n//Example\nUXCam.setUserProperty(\"role\",\"your-role\")\nUXCam.setUserProperty(\"subscription_type\",\"premium\")\nUXCam.setUserProperty(\"company_name\",\"your-company\")",
      "language": "csharp",
      "name": "Xamarin"
    },
    {
      "code": "setUserProperty(_ propertyName: String, value: Any)\n    \n//Example\nUXCam.setUserProperty(\"role\",\"your-role\")\nUXCam.setUserProperty(\"subscription_type\",\"premium\")\nUXCam.setUserProperty(\"company_name\",\"your-company\")",
      "language": "swift",
      "name": "Cordova"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "info",
  "body": "The API parameters are:\n**propertyName**: The name of the property to attach to the user.\n**value**: A value to associate with the property. String or Number are accepted for value."
}
[/block]