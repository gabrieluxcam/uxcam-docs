---
title: Sessions
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
A session is a period of time during which a user interacts with your app. It typically starts when a user launches or accesses the app and ends when users exit or minimize the app. During the session, the user's actions and interactions data and custom events can be stored which we can replay from our dashboard.

# Session Endpoints

UXCam provides **two** endpoint URLs for retrieving qualitative **(Session List)** and quantitative **(Session Analytics)** data for sessions. The endpoint URLs are as follows:

Session List: [https://api.uxcam.com/v2/session](https://api.uxcam.com/v2/session)

Session Analytics: [https://api.uxcam.com/v2/session/analytics](https://api.uxcam.com/v2/session/analytics)

# Session Data Attributes

For UXCam Data Access session APIs, all valid attributes that can be used for performing operations such as aggregations, filtering, and groupings based on their data type are listed in the table below.

## Session Attribute Table

| Attribute Category | Attribute Data Type | Attribute Name                        | Description                                                                                                                                                                                             |
| ------------------ | ------------------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DateTime           | DateTime            | date\_range                           | DateTime when the user used the app and sessions are uploaded                                                                                                                                           |
| Device             | String              | app\_version                          | Device app version recorded within a given session                                                                                                                                                      |
| Device             | String              | device\_class                         | Class of the device given by the device size based on the device height and device width                                                                                                                |
| Device             | String              | device\_id                            | Device Ids are randomly generated unique ids for user devices. More info here.                                                                                                                          |
| Device             | String              | device\_manufacturer                  | Device manufacturers recorded within a given session. E.g. Samsung and motorola.                                                                                                                        |
| Device             | String              | device\_model                         | Device name                                                                                                                                                                                             |
| Device             | String              | device\_os\_version                   | Device operating system version                                                                                                                                                                         |
| Device             | String              | device\_platform                      | Platform (iOS or Android) of the user. The Value is set to 1 for android users having a device OS as an ANDROID platform. And the value is set to 2 for iOS users having a device OS is an iOS platform |
| Session Property   | String              | event\_name                           | List of events that occurred during the session                                                                                                                                                         |
| Session Property   | String              | sdk\_version                          | UXCam SDK version on device                                                                                                                                                                             |
| Session Property   | Bool String         | session\_crashed                      | Show only sessions that are crashed / not crashed. The value set to true for crashed sessions and false for not crashed sessions                                                                        |
| Session Property   | Integer             | session\_duration                     | Total session length in seconds                                                                                                                                                                         |
| Session Property   | Integer             | session\_gesture\_count               | Total gesture on the individual session                                                                                                                                                                 |
| Session Property   | Bool String         | session\_has\_video                   | If the value is set to true, the sessions with a video are shown, and when set to false sessions having no video are shown                                                                              |
| Session Property   | Count               | session\_new\_users\_count            | Total number of new users                                                                                                                                                                               |
| Session Property   | Integer             | session\_number\_of\_user             | Count of sessions for the particular user                                                                                                                                                               |
| Session Property   | Integer             | session\_rage\_gesture\_count         | Total number of rage gestures on the session                                                                                                                                                            |
| Session Property   | Integer             | session\_responsive\_gesture\_count   | Total number of responsive gestures on the session                                                                                                                                                      |
| Session Property   | Integer             | session\_screen\_count                | Total number of screens visited on the session                                                                                                                                                          |
| Session Property   | list                | session\_screen\_list                 | List of screens visited during the session                                                                                                                                                              |
| Session Property   | Integer             | session\_unique\_screen\_count        | Total number of screen visited on the session                                                                                                                                                           |
| Session Property   | Count               | session\_unique\_user\_count          | Total users                                                                                                                                                                                             |
| Session Property   | Integer             | session\_unresponsive\_gesture\_count | Total number of unresponsive gestures on the session                                                                                                                                                    |
| Session Property   | DateTime String     | session\_uploaded\_month              | Distribution of sessions by month (relevant to grouping parameter only)                                                                                                                                 |
| Session Property   | DateTime String     | session\_uploaded\_week               | Distribution of sessions by week (Mon-Sun) (relevant to grouping parameter only)                                                                                                                        |
| Session Property   | DateTime String     | session\_uploadedon\_day              | Distribution of sessions by days (relevant to grouping parameter only)                                                                                                                                  |
| Session Property   | String              | session\_id                           | UXCam Session ID of user                                                                                                                                                                                |
| User               | String              | device\_city                          | Country’s city name based on device IP address. Read more about IP Address.                                                                                                                             |
| User               | String              | device\_country                       | Country name based on device IP address                                                                                                                                                                 |
| User               | String              | uxcamuserid                           | UXCam User ID of user                                                                                                                                                                                   |
| User               | String              | user\_name                            | Randomly assigned alias for users. Note: This is not the real user name, alias, or ID. Learn more.                                                                                                      |
| User               | JSON                | user\_custom\_property                | Additional custom properties attached to sessions                                                                                                                                                       |

<br />

### Web Only:

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Attribute




         Category
      </th>

      <th>
        Attribute




         Data Type
      </th>

      <th>
        Attribute Name
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        User
      </td>

      <td>
        JSON
      </td>

      <td>
        user\_first\_utm
      </td>

      <td>
        The UTM of the first session of the user.
      </td>
    </tr>

    <tr>
      <td>
        User
      </td>

      <td>
        String
      </td>

      <td>
        user\_first\_referer
      </td>

      <td>
        The referrer URL of the first session of the user.
      </td>
    </tr>

    <tr>
      <td>
        User
      </td>

      <td>
        String
      </td>

      <td>
        user\_first\_screen\_name
      </td>

      <td>
        First page the user landed on.
      </td>
    </tr>

    <tr>
      <td>
        User
      </td>

      <td>
        String
      </td>

      <td>
        user\_first\_referer\_domain
      </td>

      <td>
        The referring domain of the first session of the user.
      </td>
    </tr>

    <tr>
      <td>
        Session
      </td>

      <td>
        String
      </td>

      <td>
        browser\_name
      </td>

      <td>
        User’s browser name in a session  
      </td>
    </tr>

    <tr>
      <td>
        Session
      </td>

      <td>
        String
      </td>

      <td>
        browser\_version
      </td>

      <td>
        User’s browser version in a session
      </td>
    </tr>

    <tr>
      <td>
        Session
      </td>

      <td>
        String
      </td>

      <td>
        referer
      </td>

      <td>
        URL that referred the user to the current session
      </td>
    </tr>

    <tr>
      <td>
        Session
      </td>

      <td>
        String
      </td>

      <td>
        referer\_domain
      </td>

      <td>
        Domain of the referrer URL in a session  
      </td>
    </tr>

    <tr>
      <td>
        Session
      </td>

      <td>
        JSON
      </td>

      <td>
        utm
      </td>

      <td>
        UTM parameters of the Session's Landing page.
      </td>
    </tr>

    <tr>
      <td>
        Session
      </td>

      <td>
        String
      </td>

      <td>
        device\_os\_name
      </td>

      <td>
        Device OS name from which sessions are recorded
      </td>
    </tr>

    <tr>
      <td>
        Session
      </td>

      <td>
        String
      </td>

      <td>
        device\_type
      </td>

      <td>
        Device type from which sessions are recorded
      </td>
    </tr>
  </tbody>
</Table>

## List Sessions

A session list provides qualitative data, including details about the device, interactions of users within the session, personalized user properties, and location data.

### Request Parameters

This list session endpoint supports the following query parameters apart from authentication, as given in the URL structure below.

* [pagination](https://help.uxcam.com/hc/en-us/articles/15844412117401)
* [filters](https://help.uxcam.com/hc/en-us/articles/15844412117401)

For more information on request parameters see the [Query parameters](https://developer.uxcam.com/docs/query-parameters) section

**Structure of API URL:**

```asp Example
https://api.uxcam.com/v2/session?appid=<appid>&apikey=<apikey>&filters=<filters>&page=<page number>&page_size=<number of data in single request>
```

To authenticate the list sessions API, appid and API key are required. See [authentication](https://developer.uxcam.com/docs/data-access-api) for more details.

[Example Link](https://api.uxcam.com/v2/session?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&page=1\&page_size=50)

### Response Structure

The List session API provides information about sessions in several key-value pairs grouped under different keys: session properties are under the "**property**" key, device information is under the "**device**" key, user properties are under the "**user**" key, and device location information is under the "**location**" key.

Here in the response, their **video** key contains the session video link and this link has an expiry date of **one week**.

```coffeescript Example
{
  "success": true,
  "data": [
    {
      "sessionId": "60f7dd4efd9c2f001169bb96",
      "video": "Video Link”,

      "sessionNumber": 20, # nth number of session
      "url": "https://app.uxcam.com/app/60f6c0b8b97ba419120b82eb/sessions/list/1/60f7dd4efd9c2f001169bb96",
      "property": {
        "uploadedServerTime": "2023-01-16 10:43:47",
        "deviceLocalDateTime": "2021-09-10 10:43:47",
        "verifiedServerTime": "2021-07-21 08:39:42",
        "offlineRecorded": false,
        "hasVideo": true,
        "isCrashed": false,
        "durationSec": 55.009,
        "totalGesture": 26,
        "totalScreen": 17,
        "uniqueScreensCount": 5,
        "rageGestureCount": 0,
        "networkType": "wifi"
      },
      "user": {
          "kUXCam_UserIdentity": "U#5066",
          "gender": "male",
          "exp_var_key": "test_2",
          "age_group": "10-19",
          "alias": "Isaias Altenwerth",
          "language": "English",
          "loyalty_card": "no",
          "acqn_source": "none",
          "uxcamuserid": "60f7dd46972a633e88696d6b"
      },


      "device": {
        "carrierCode": "42901",
        "carrierName": "Verizon Wireless",
        "osName": " O_MR1",
        "appVersion": "1.5",
        "sdkVersion": "3.3.5",
        "osVersion": "8.1",
        "language": "English",
        "country": "USA",
        "totalStorageInKB": 11289,
        "totalRamInKB": 2815,
        "freeRamInKb": null,
        "dpi": -1,
        "height": 2340,
        "width": 1080,
        "type": "Phone",
        "class": "Android Large",
        "producer": "Huawei",
        "model": "JKM-LX1",
        "deviceId": "03c1e123941a19ec",
        "isNotificationEnabled": null,
        "isRooted": false,
        "platform": "android"
      },
      "location": {
        "countryCode": "US",
        "city": "Albany",
        "longitude": -73.7987,
        "latitude": 42.6664
      }
    }
  ]
}
```

### Example Requests

List sessions from the country USA:

```
https://api.uxcam.com/v2/session?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[{"attribute":"device_country","operator":"equal","value":"USA"}]
```

## Analyze Sessions

This endpoint for analyzing session data offers aggregated numerical data that can be utilized to create statistical graphs and gain valuable insights.

### Request Parameters

This session analytics endpoint supports the following query parameters as given in the URL structure below.

* [pagination](https://developer.uxcam.com/docs/query-parameters#pagination)
* [filters]()
* group\_by
* aggregation
* comparison

For more information on request parameters see the Query parameters section

**Structure of API URL:**

```
https://api.uxcam.com/v2/session/analytics?appid=<appid>&apikey=<apikey>&filters=<filters>&aggregation=<aggregation>&group_by=<groupings>&page=<page number>&page_size=<number of data in single request>

```

To authenticate the event analytics API, app id and API key are required. See [authentication](https://developer.uxcam.com/docs/data-access-api) for more details.

[Example](https://api.uxcam.com/v2/session/analytics?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&page=1\&page_size=100)

### Response Structure

The session analytics API provides aggregated data of session based on the session query which is grouped by grouping attributes. It gives us all of the session aggregate data in the output so it can be used to plot statistical charts.

```coffeescript
{

  "success": true,

  "data": [

    {

      "device_model": "SM-C7100",

      "session_count": 1577,

      "session_unique_user_count": 754,

      "session_new_users_count": 24,

      "avg_session_gesture_count": 22.779327837666454,

      "avg_session_rage_gesture_count": 0.22447685478757134,

      "avg_session_responsive_gesture_count": 18.566265060240966,

      "avg_session_unresponsive_gesture_count": 4.213062777425492,

      "avg_session_duration": 92.12662,

      "avg_session_unique_screen_count": 5.658211794546608,

      "dashboard_link": "https://app.uxcam.com/app/60f6c0b8b97ba419120b82eb/sessions/list/1?device_model={\"o\":\"in\",\"v\":[\"SM-C7100\"], \"tb\": \"session\"}&date_range={\"o\": \"between_dates\", \"v\": {\"lower\": \"2022-12-17T10:25:08Z\", \"upper\": \"2023-01-16T10:25:08Z\"}, \"tb\": \"session\"}&device_country={\"o\":\"equal\",\"v\":\"USA\", \"tb\": \"session\"}"

    },

    {

      "device_model": "SM-J730",

      "session_count": 1207,

      "session_unique_user_count": 594,

      "session_new_users_count": 12,

      "avg_session_gesture_count": 22.850041425020713,

      "avg_session_rage_gesture_count": 0.24772162386081192,

      "avg_session_responsive_gesture_count": 18.566694283347143,

      "avg_session_unresponsive_gesture_count": 4.283347141673571,

      "avg_session_duration": 92.86992,

      "avg_session_unique_screen_count": 5.540182270091135,

      "dashboard_link": "https://app.uxcam.com/app/60f6c0b8b97ba419120b82eb/sessions/list/1?device_model={\"o\":\"in\",\"v\":[\"SM-J730\"], \"tb\": \"session\"}&date_range={\"o\": \"between_dates\", \"v\": {\"lower\": \"2022-12-17T10:25:08Z\", \"upper\": \"2023-01-16T10:25:08Z\"}, \"tb\": \"session\"}&device_country={\"o\":\"equal\",\"v\":\"USA\", \"tb\": \"session\"}"

    }

  ]}
```

### Example Requests

Session from device country USA distributed by device model:

To get session analytics data (**quantitative**) which is grouped with **device model** from country **USA**. The structure of the API URL.

```
https://api.uxcam.com/v2/session/analytics?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[{"attribute": "device_country","operator": "equal", "value":"USA"}]&group_by=[{"attribute": "device_model", "max_group_number": 50}]&page=1&page_size=100
```
