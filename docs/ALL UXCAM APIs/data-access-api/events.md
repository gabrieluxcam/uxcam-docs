---
title: Events
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
When a user performs an action or some actions are triggered within an app, it generates event data. This data can be sorted and organized according to the characteristics listed in the table. Additionally, every event data instance can be classified based on all session attributes.

For example, when a user taps the login button in the app, it is considered a tap gesture (automatically captured). Additionally, SDK developers can send events and event properties to UXCam to gather more insights about the users' behavior. To send custom events, follow [this guide.](https://developer.uxcam.com/docs/send-events)

## Event Endpoints

UXCAM provides two endpoint URLs for retrieving qualitative (**Event List**) and quantitative (**Event Analytics**) data for events. The endpoint URLs are:

Event List: [https://api.uxcam.com/v2/event](https://api.uxcam.com/v2/event)

Event Analytics: [https://api.uxcam.com/v2/event/analytics](https://api.uxcam.com/v2/event/analytics)

## Event Data Attributes

For UXCam Data Access event APIs, all valid attributes that can be used for performing operations such as aggregations, filterings, and groupings based on their data type are listed in the table below.

### Event Attribute Table

| Attribute Category | Attribute Data Type      | Attribute Name                        | Description                                                                                                                                                                                              |
| ------------------ | ------------------------ | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DateTime           | DateTime                 | date\_range                           | DateTime when the user used the app and sessions/events are uploaded                                                                                                                                     |
| Device             | String                   | app\_version                          | Device app version recorded within a given session.                                                                                                                                                      |
| Device             | String                   | device\_class                         | Class of the device given by the device size.                                                                                                                                                            |
| Device             | String                   | device\_id                            | Device Ids are randomly generated unique ids for user devices. More info here.                                                                                                                           |
| Device             | String                   | device\_manufacturer                  | Device manufacturers recorded within a given session. E.g. Samsung and motorola.                                                                                                                         |
| Device             | String                   | device\_model                         | Device name                                                                                                                                                                                              |
| Device             | String                   | device\_os\_version                   | Device operating system version                                                                                                                                                                          |
| Device             | String                   | device\_platform                      | Platform (iOS or Android) of the user. The value is set to 1 for android users having a device OS as an ANDROID platform. And the value is set to 2 for iOS users having a device OS is an iOS platform. |
| Session Property   | String                   | sdk\_version                          | UXCam SDK version on device                                                                                                                                                                              |
| Session Property   | Bool String              | session\_crashed                      | Show only sessions that are crashed / not crashed. The value is set to true for crashed sessions and false for not crashed sessions.                                                                     |
| Session Property   | Integer                  | session\_duration                     | Total session length in seconds                                                                                                                                                                          |
| Session Property   | Integer                  | session\_gesture\_count               | Total gesture on the individual session                                                                                                                                                                  |
| Session Property   | Bool String (true/false) | session\_has\_video                   | Is there a video for the session? Value is set to true and false for sessions with no video.                                                                                                             |
| Session Property   | Count                    | session\_new\_users\_count            | Total new users                                                                                                                                                                                          |
| Session Property   | Integer                  | session\_number\_of\_user             | Count of sessions for the particular user. View user 10th session with UXCamuserid                                                                                                                       |
| Session Property   | Integer                  | session\_rage\_gesture\_count         | Total number of rage gestures in the session                                                                                                                                                             |
| Session Property   | Integer                  | session\_responsive\_gesture\_count   | Total number of responsive gestures on the session                                                                                                                                                       |
| Session Property   | Integer                  | session\_screen\_count                | Total number of screens visited on the session                                                                                                                                                           |
| Session Property   | list                     | session\_screen\_list                 | List of screens visited during the session                                                                                                                                                               |
| Session Property   | Integer                  | session\_unique\_screen\_count        | Total number of screens visited on the session                                                                                                                                                           |
| Session Property   | Integer                  | session\_unresponsive\_gesture\_count | Total number of unresponsive gestures during the session                                                                                                                                                 |
| Event              | DateTime String          | event\_uploaded\_month                | Distribution of events by month (relevant to grouping parameter only)                                                                                                                                    |
| Event              | DateTime String          | event\_uploaded\_week                 | Distribution of events by week(Mon-Sun) (relevant to grouping parameter only)                                                                                                                            |
| Event              | DateTime String          | event\_uploadedon\_day                | Distribution of events by days (relevant to grouping parameter only)                                                                                                                                     |
| User               | String                   | device\_city                          | Country city name based on device IP address. Read for more details about IP Address                                                                                                                     |
| User               | String                   | device\_country                       | Country name based on device IP address                                                                                                                                                                  |
| User               | String                   | uxcamuserid                           | The UXCam User ID assigned to the user                                                                                                                                                                   |
| User               | String                   | user\_name                            | Randomly assigned alias for users. Note: This is not the real user name, alias, or ID. More info here.                                                                                                   |
| User               | JSON                     | user\_custom\_property                | Additional custom properties attached to sessions                                                                                                                                                        |
| Event              | String                   | event\_name                           | Event triggered by users on sessions                                                                                                                                                                     |
| Event              | String                   | event\_screen\_name                   | Screen name on which event happened                                                                                                                                                                      |
| Event              | JSON                     | event\_custom\_property               | Additional properties attach for events on sessions                                                                                                                                                      |

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

## List Events

### Request Parameters

This list events endpoint supports the following query parameters apart from authentication as given in the URL structure below.

* [Pagination](https://developer.uxcam.com/docs/query-parameters#pagination)
* Filters

For more information on request parameters see the [Query parameters](https://developer.uxcam.com/docs/query-parameters) section

**Structure of API URL:**

```
https://api.uxcam.com/v2/event?appid=<appid>&apikey=<apikey>&filters=<filters>&page=<page number>&page_size=<number of data in single request>
```

To authenticate the List event API, App ID and API key are required. See [authentication](https://developer.uxcam.com/docs/data-access-api) for more details.

[Example](https://api.uxcam.com/v2/event?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&page=1\&page_size=50)

### Response Structure

The List event API provides information about events in several key-value pairs grouped under different keys: session properties data are under the "**sessionProperty**" key, device information is under the "**device**" key, user properties are under the "**userProperty**" key, and event custom properties information is under the "**eventProperty**" key.

```coffeescript Example
{
  "success": true,
  "data": [
    {
      "eventId": "9d0cb696-65e2-4783-9556-352bfd71009f",
      "eventName": "view product category",
      "eventScreen": "CategoryActivity",
      "eventDate": "2023-01-22T17:31:57Z",
      "eventPropertyTime": 75.926,
      "url": "https://app.uxcam.com/app/60f6c0b8b97ba419120b82eb/sessions/list/1/60f7dd4d46c872001188eb3f",
      "sessionProperty": {
        "sessionId": "60f7dd4d46c872001188eb3f",
        "hasVideo": true,
        "isCrashed": true,
        "durationSec": 98.773,
        "totalGesture": 42,
        "totalScreen": 39,
        "uniqueScreensCount": 7,
        "networkType": "wifi",
        "sessionNumber": 19,
        "country": "USA"
      },
      "userProperty": {
        "kUXCam_UserIdentity": "U#5066",
        "gender": "male",
        "exp_var_key": "test_2",
        "age_group": "10-19",
        "language": "English",
        "loyalty_card": "no",
        "acqn_source": "none",
        "uxcamuserid": "60f7dd46972a633e88696d6b"
      },
      "eventProperty": {
        "category": "Vegetarian, vegan & meat free"
      },
      "device": {
        "model": "JKM-LX1",
        "deviceId": "03c1e123941a19ec",
        "platform": 1,
        "appVersion": "1.5",
        "osVersion": "8.1",
        "producer": "Huawei",
        "class": "Android Large"
      }
    }
  ]
}
```

### Example Requests

List events from the android devices:

```
https://api.uxcam.com/v2/event?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[{"attribute":"device_platform","operator":"equal","value":"1"}]
```

## Analyze Events

This endpoint for analyzing event data offers aggregated numerical data that can be utilized to create statistical graphs and gain valuable insights.

### Request Parameters

This event analytics endpoint supports the following query parameters as given in the URL structure below.

* pagination
* filters
* group\_by
* aggregation
* comparison

For more information on request parameters see the [Query parameters](https://developer.uxcam.com/docs/query-parameters) section

**Structure of API URL:**

```
https://api.uxcam.com/v2/event/analytics?appid=<appid>&apikey=<apikey>&filters=<filters>&aggregation=<aggregation>&group_by=<groupings>&page=<page number>&page_size=<number of data in single request>

```

To authenticate the event analytics API, app id and API key are required. See [authentication](https://developer.uxcam.com/docs/data-access-api) for more details.

[Example](https://api.uxcam.com/v2/event/analytics?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&page=1\&page_size=100)

### Response Structure

The event analytics API provides aggregated data of sessions based on the event query, which is grouped by grouping attributes. It gives us all of the session aggregate data in the output so it can be used to plot statistical charts.

```coffeescript Example
{
  "success": true,
  "data": [
    {
      "event_count": 165159,
      "event_unique_user_count": 2993,
      "event_unique_session_count": 12137,
      "dashboard_link": "https://app.uxcam.com/app/60f6c0b8b97ba419120b82eb/events/list/1?date_range={\"o\": \"between_dates\", \"v\": {\"lower\": \"2022-12-24T08:45:42Z\", \"upper\": \"2023-01-23T08:45:42Z\"}, \"tb\": \"event\"}"
    }
  ]
}
```

### Example Requests

Events from device country USA distributed by device model:

To get event analytics data (**quantitative**) which is grouped with **device model** from country **USA**. We need to make the following changes to the structure of the API URL from above.

```
https://api.uxcam.com/v2/event/analytics?appid=60f6c0b8b97ba419120b82eb&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618&filters=[{"attribute": "device_country","operator": "equal", "value":"USA"}]&group_by=[{"attribute": "device_model", "max_group_number": 50}]&page=1&page_size=100
```
