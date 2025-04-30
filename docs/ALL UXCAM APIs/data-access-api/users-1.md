---
title: Users
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
An entity that engages with an application is referred to as a user. A user can be linked to a device by their device ID. If a singular user has multiple devices, the user can be tracked across all devices by utilizing API calls.

## User Endpoints

UXCam Data Access API provides two endpoint URLs for retrieving both qualitative (List User) and quantitative (Analyze User) data for users. The endpoint URLs are:

List User: <https://api.uxcam.com/v2/user>

Analyze User: <https://api.uxcam.com/v2/user/analytics>

## User Data Attributes

For UXCam Data Access user APIs, all valid attributes that can be used for performing operations such as aggregations, filterings, and groupings based on their data type are listed in the table below.

### Users Attribute Table

| Attribute Category | Attribute Data Type | Attribute Name                  | Description                                                                                                                                                                                              |
| ------------------ | ------------------- | ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DateTime           | DateTime            | date_range                      | DateTime when the user used the app                                                                                                                                                                      |
| DateTime           | DateTime            | user_first_seen_on              | DateTime when the user used the app first                                                                                                                                                                |
| Device             | string              | app_version                     | App version on the device                                                                                                                                                                                |
| Device             | string              | device_class                    | Class of the device given by the device size                                                                                                                                                             |
| Device             | string              | device_id                       | Device Ids are randomly generated unique ids for user devices. For more detail [Link]                                                                                                                    |
| Device             | string              | device_manufacturer             | Device manufacturers recorded within a given user. E.g. Samsung and motorola                                                                                                                             |
| Device             | string              | device_model                    | Device name                                                                                                                                                                                              |
| Device             | string              | device_os_version               | Device OS version recorded within a given user                                                                                                                                                           |
| Device             | string              | device_platform                 | Platform (iOS or Android) of the user. The value is set to 1 for android users having a device OS as an ANDROID platform, and the value is set to 2 for iOS users having a device OS as an iOS platform. |
| Device             | string              | sdk_version                     | UXCam SDK version on app                                                                                                                                                                                 |
| Engagement         | Integer             | user_gesture_count              | Total number of gestures by the user                                                                                                                                                                     |
| Engagement         | Integer             | user_rage_gesture_count         | Total number of rage gestures on the user                                                                                                                                                                |
| Engagement         | Integer             | user_responsive_gesture_count   | Total number of responsive gestures by the user                                                                                                                                                          |
| Engagement         | Integer             | user_session_screen_count       | Total number of screens visited by the user                                                                                                                                                              |
| Engagement         | Integer             | user_session_duration           | Total time spent in seconds by the user                                                                                                                                                                  |
| Engagement         | Integer             | user_unresponsive_gesture_count | Total number of unresponsive gestures by the user                                                                                                                                                        |
| Session Property   | string              | event_name                      | Event triggered by users on sessions                                                                                                                                                                     |
| Session Property   | list                | session_screen_list             | List of screens visited during the session                                                                                                                                                               |
| Session Property   | DateTime String     | session_uploaded_month          | Distribution of users by month (relevant to grouping parameter only)                                                                                                                                     |
| Session Property   | DateTime String     | session_uploaded_week           | Distribution of users by week(Mon-Sun) (relevant to grouping parameter only)                                                                                                                             |
| Session Property   | DateTime String     | session_uploadedon_day          | Distribution of users by days (relevant to grouping parameter only)                                                                                                                                      |
| User               | string              | device_city                     | Country city name based on device IP address. For more details on IP Address [Link]                                                                                                                      |
| User               | string              | device_country                  | Country name based on device IP address                                                                                                                                                                  |
| User               | JSON                | user_custom_property            | Additional properties attached for users on sessions                                                                                                                                                     |
| User               | string              | user_name                       | Randomly assigned alias for users. Note: This is not the real user name, alias, or ID. For more detail [Link]                                                                                            |
| User               | string              | uxcamuserid                     | UXCam User ID of user                                                                                                                                                                                    |

<br />

### Web Only:

[block:parameters]
{
  "data": {
    "h-0": "Attribute  \n Category",
    "h-1": "Attribute  \n Data Type",
    "h-2": "Attribute Name",
    "h-3": "Description",
    "0-0": "User",
    "0-1": "JSON",
    "0-2": "user_first_utm",
    "0-3": "The UTM of the first session of the user.",
    "1-0": "User",
    "1-1": "String",
    "1-2": "user_first_referer",
    "1-3": "The referrer URL of the first session of the user.",
    "2-0": "User",
    "2-1": "String",
    "2-2": "user_first_screen_name",
    "2-3": "First page the user landed on.",
    "3-0": "User",
    "3-1": "String",
    "3-2": "user_first_referer_domain",
    "3-3": "The referring domain of the first session of the user.",
    "4-0": "Session",
    "4-1": "String",
    "4-2": "browser_name",
    "4-3": "User’s browser name in a session  ",
    "5-0": "Session",
    "5-1": "String",
    "5-2": "browser_version",
    "5-3": "User’s browser version in a session",
    "6-0": "Session",
    "6-1": "String",
    "6-2": "referer",
    "6-3": "URL that referred the user to the current session",
    "7-0": "Session",
    "7-1": "String",
    "7-2": "referer_domain",
    "7-3": "Domain of the referrer URL in a session  ",
    "8-0": "Session",
    "8-1": "JSON",
    "8-2": "utm",
    "8-3": "UTM parameters of the Session's Landing page.",
    "9-0": "Session",
    "9-1": "String",
    "9-2": "device_os_name",
    "9-3": "Device OS name from which sessions are recorded",
    "10-0": "Session",
    "10-1": "String",
    "10-2": "device_type",
    "10-3": "Device type from which sessions are recorded"
  },
  "cols": 4,
  "rows": 11,
  "align": [
    "left",
    "left",
    "left",
    "left"
  ]
}
[/block]


## List Users

A user list provides qualitative data, including details about the device, user interactions, personalized user properties, and location data.

### Request Parameters

Now that you have explored the data attributes and query parameters, The list user endpoint supports the following query parameters as given in the URL structure below. 

- [Pagination](https://developer.uxcam.com/docs/query-parameters#pagination)
- Filters

For more information on request parameters see the [Query parameters ](https://developer.uxcam.com/docs/query-parameters)section

**Structure of API URL:**

```
https://api.uxcam.com/v2/user?appid=<appid>&apikey=<apikey>&filters=<filters>&page=<page number>&page_size=<number of data in single request>

```

To authenticate the List event API, App ID and API key are required. See [authentication](https://developer.uxcam.com/docs/data-access-api) for more details.

[Example](https://api.uxcam.com/v2/user?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&page=1&page_size=50)

### Response Structure

The response is in JSON format and includes a data array with multiple objects enclosed in curly brackets. Each object can be thought of as a new row and is separated by a comma. Each object includes several properties, which are represented as key-value pairs.

The List user API provides information about a user in several key-value pairs grouped under different keys: user custom properties are under the "**property**" key, user device information is under the "**device**" key, user engagement metrics are under the "**usage**" key, and user location information is under the "**location**" key.

```coffeescript Example
{

  "success": true,

  "data": [

    {

      "uxcamUserId": "60f7e1a43d4dcd370b7e0b02",

      "userId": "U#744",

      "url": "https://app.uxcam.com/app/60f6c0b8b97ba419120b82eb/users/list/1/60f7e1a43d4dcd370b7e0b02",

      "property": {

        "alias": "Garfield Gerlach",

        "gender": "Male",

        "language": "English",

        "age_group": "71-80",

        "acqn_source": "none",

        "exp_var_key": "test_3",

        "loyalty_card": "no",

        "kUXCam_UserIdentity": "U#744"

      },

      "usage": {

        "registeredOn": "2022-11-12T06:48:43Z",

        "lastseenOn": "2023-01-11T06:27:48Z",

        "totaluser": 12,

        "totaluserTime": 475.85600000000005,

        "totalGesture": 152,

        "screenCount": 92,

        "eventCount": 49,

        "rageGestureCount": 0,

        "responsiveGestureCount": 108,

        "unresponsiveGestureCount": 44

      },

      "location": {

        "countryCode": "NP",

        "city": "Kathmandu",

        "country": "Nepal"

      },

      "device": {

        "model": "SM-A525F",

        "deviceId": "ee294c9b1d063333",

        "platform": "android",

        "appVersion": "1.2",

        "osVersion": "13"

      }

    }

  ]

}

```

### Example Requests

List users from the country USA:

```
https://api.uxcam.com/v2/user?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[{"attribute":"device_country","operator":"equal","value":"USA"}]

```

## Analyze Users

This endpoint for analyzing user data offers aggregated numerical data that can be utilized to create statistical graphs and gain valuable insights.

### Request Parameters

This user analytics endpoint supports the following query parameters as given in the URL structure below.

- pagination
- filters
- group_by
- aggregation
- comparison

For more information on request parameters see the [Query parameters](https://developer.uxcam.com/docs/query-parameters) section.

**Structure of API URL:**

```
https://api.uxcam.com/v2/user/analytics?appid=<appid>&apikey=<apikey>&filters=<filters>&aggregation=<aggregation>&group_by=<groupings>&page=<page number>&page_size=<number of data in single request>
```

To authenticate the event analytics API, app id and API key are required. See [authentication](https://developer.uxcam.com/docs/data-access-api) for more details.

[Example](https://api.uxcam.com/v2/user/analytics?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&page=1&page_size=100)

### Response Structure

The user analytics API provides aggregated data of users based on the user’s query which is grouped by grouping attributes. It gives us all of the user aggregate data in the output so it can be used to plot statistical charts.

```coffeescript Example
{
  "success": true,
  "data": [
    {
      "user_count": 2993,
      "session_new_users_count": 173,
      "avg_user_session_count": 16.686505020818025,
      "avg_user_session_duration": 1402.0168,
      "avg_user_rage_gesture_count": 3.4235447791656464,
      "avg_user_event_count": 201.1265409421177,
      "avg_user_session_screen_count": 257.78888072495715,
      "dashboard_link": "https://app.uxcam.com/app/60f6c0b8b97ba419120b82eb/users/list/1?date_range={\"o\": \"between_dates\", \"v\": {\"lower\": \"2022-12-24T08:51:21Z\", \"upper\": \"2023-01-23T08:51:21Z\"}, \"tb\": \"session\"}"
    }
  ]
}
```

[Example](https://api.uxcam.com/v2/user/analytics?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[%7B%22attribute%22:%20%22device_country%22,%22operator%22:%20%22equal%22,%20%22value%22:%22USA%22%7D]&group_by=[%7B%22attribute%22:%20%22device_model%22,%20%22max_group_number%22:%2050%7D]&page=1&page_size=100)

### Example Requests

- Usage for a given location compared to the past:

How many users are in a given country (for instance USA) for the app in the last month, and how did this change compared to the month before?

```
https://api.uxcam.com/v2/user/analytics?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[{"attribute":"device_country","operator":"equal","value":"USA"}]&comparison=1
```

- Users distribution by device manufacturer

What is the most commonly used device manufacturer observed in the generated users? Or what is the distribution of users by the device manufacturer for Jan 2023?

```
https://api.uxcam.com/v2/user/analytics?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[{"attribute":"date_range","operator":"between_dates","value":{"lower":"2023-01-01","upper":"2023-01-20"}}]&group_by=[{"attribute":"device_manufacturer"}]&aggregation=[{"attribute":"user_count","operator":""}]

```