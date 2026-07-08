---
title: Session Endpoints
deprecated: false
hidden: true
metadata:
  robots: index
---
A **session** represents the period during which a user actively engages with your app — typically commencing at app launch and concluding on exit or minimise. Throughout this timeframe, user interactions and custom events are logged for potential replay via the UXCam dashboard.

## Session Endpoints

UXCam offers two endpoints for retrieving session data:

**Session List** — qualitative, per-session records:

`POST /api/data-access/v1/session`

**Session Analytics** — aggregated, grouped numbers:

`POST /api/data-access/v1/session/analytics`

## Session Data Attributes

The following table outlines all valid attributes for performing aggregations, filtering, and grouping operations, organised by data type.

| Attribute Category | Data Type       | Attribute Name                       | Description                                                   |
| ------------------ | --------------- | ------------------------------------ | ------------------------------------------------------------- |
| DateTime           | DateTime        | `date_range`                         | DateTime when the user used the app and sessions are uploaded |
| Device             | String          | `app_version`                        | Device app version recorded within a given session            |
| Device             | String          | `device_class`                       | Class based on device size (height and width)                 |
| Device             | String          | `device_id`                          | Randomly generated unique device identifier                   |
| Device             | String          | `device_manufacturer`                | Device manufacturer (e.g. Samsung, Motorola)                  |
| Device             | String          | `device_model`                       | Device name                                                   |
| Device             | String          | `device_os_version`                  | Operating system version                                      |
| Device             | String          | `device_platform`                    | Platform: 1 = Android, 2 = iOS, 3 = Web                       |
| Session Property   | String          | `event_name`                         | List of events that occurred during the session               |
| Session Property   | String          | `sdk_version`                        | UXCam SDK version on device                                   |
| Session Property   | Bool String     | `session_crashed`                    | Show only sessions that crashed / did not crash               |
| Session Property   | Integer         | `session_duration`                   | Total session length in seconds                               |
| Session Property   | Integer         | `session_gesture_count`              | Total gestures in an individual session                       |
| Session Property   | Bool String     | `session_has_video`                  | Video availability indicator                                  |
| Session Property   | Count           | `session_new_users_count`            | Total number of new users                                     |
| Session Property   | Integer         | `session_number_of_user`             | Count of sessions for the particular user                     |
| Session Property   | Integer         | `session_rage_gesture_count`         | Total rage gestures in the session                            |
| Session Property   | Integer         | `session_responsive_gesture_count`   | Total responsive gestures in the session                      |
| Session Property   | Integer         | `session_screen_count`               | Total screens visited in the session                          |
| Session Property   | list            | `session_screen_list`                | List of screens visited during the session                    |
| Session Property   | Integer         | `session_unique_screen_count`        | Total unique screens visited in the session                   |
| Session Property   | Count           | `session_unique_user_count`          | Total users                                                   |
| Session Property   | Integer         | `session_unresponsive_gesture_count` | Total unresponsive gestures in the session                    |
| Session Property   | DateTime String | `session_uploaded_month`             | Distribution of sessions by month                             |
| Session Property   | DateTime String | `session_uploaded_week`              | Distribution of sessions by week (Mon–Sun)                    |
| Session Property   | DateTime String | `session_uploadedon_day`             | Distribution of sessions by day                               |
| Session Property   | String          | `session_id`                         | UXCam Session ID                                              |
| User               | String          | `device_city`                        | City name based on device IP address                          |
| User               | String          | `device_country`                     | Country name based on device IP address                       |
| User               | String          | `uxcamuserid`                        | UXCam User ID                                                 |
| User               | String          | `user_name`                          | Randomly assigned alias for the user                          |
| User               | JSON            | `user_custom_property`               | Additional custom properties attached to sessions             |

### Web-only Attributes

These additional attributes are available for web sessions.

| Attribute Category | Data Type | Attribute Name              | Description                                       |
| ------------------ | --------- | --------------------------- | ------------------------------------------------- |
| User               | JSON      | `user_first_utm`            | The UTM of the user's first session               |
| User               | String    | `user_first_referer`        | The referrer URL of the user's first session      |
| User               | String    | `user_first_screen_name`    | First page the user landed on                     |
| User               | String    | `user_first_referer_domain` | The referring domain of the user's first session  |
| Session            | String    | `browser_name`              | User's browser name in a session                  |
| Session            | String    | `browser_version`           | User's browser version in a session               |
| Session            | String    | `referer`                   | URL that referred the user to the current session |
| Session            | String    | `referer_domain`            | Domain of the referrer URL in a session           |
| Session            | JSON      | `utm`                       | UTM parameters of the session's landing page      |
| Session            | String    | `device_os_name`            | Device OS name from which sessions are recorded   |
| Session            | String    | `device_type`               | Device type from which sessions are recorded      |

## List Sessions

This endpoint delivers qualitative data encompassing device details, user interactions, personalised properties, and location information. Sections are grouped under `property`, `user`, `device`, and `location`.

### Request Parameters

Send a JSON body; the API key rides in the `X-Api-Key` header.

- `app_id` — required; the app to read.
- `filters` — optional filter objects; omit for the default last-30-days window.
- `show_only` — sections to return. Omit for the default `["property"]`; the example below requests all four (`property`, `user`, `device`, `location`).
- `page_size` — records per page, `1`–`2000` (default `50`).
- `cursor` — opaque cursor for the next page; omit for the first page.
- `with_video` — set `true` for a time-limited signed replay link per session.

Full field reference: [Query Parameters](doc:query-parameters).

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/session \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","show_only":["property","user","device","location"],"filters":[{"attribute":"device_country","operator":"equal","value":"USA"}],"page_size":50}'
```

### Response Structure

Response data organises into keys: session properties under `property`, device information under `device`, user properties under `user`, and location data under `location`. When `with_video` is set, `video` holds a time-limited signed replay link; otherwise it is `null`. The lookup is best-effort — if a replay link can't be produced for a session, that row's `video` is `null` and the request still succeeds.

```json
{
  "success": true,
  "data": [
    {
      "sessionId": "60f7dd4efd9c2f001169bb96",
      "sessionNumber": 20,
      "url": "https://app.uxcam.com/app/YOUR_APP_ID/sessions/list/1/60f7dd4efd9c2f001169bb96",
      "video": null,
      "user": {
        "kUXCam_UserIdentity": "U#5066",
        "gender": "male",
        "language": "English",
        "uxcamuserid": "60f7dd46972a633e88696d6b"
      },
      "property": {
        "uploadedServerTime": "2026-06-28T10:43:47Z",
        "deviceLocalDateTime": "2026-06-28T12:43:47Z",
        "verifiedServerTime": "2026-06-28T10:43:42Z",
        "durationSec": 55.009,
        "totalGesture": 26,
        "totalScreen": 17,
        "uniqueScreensCount": 5,
        "rageGestureCount": 0,
        "hasVideo": true,
        "uniqueScreens": ["HomeRoute", "ProductListRoute", "CheckOutRoute"],
        "offlineRecorded": false,
        "isCrashed": false,
        "networkType": "wifi"
      },
      "device": {
        "osName": "O_MR1", "appVersion": "1.5", "sdkVersion": "3.3.5",
        "osVersion": "8.1", "language": "English", "country": "USA",
        "dpi": -1, "height": 2340, "width": 1080, "type": "Phone",
        "producer": "Huawei", "deviceId": "03c1e123941a19ec",
        "carrierCode": "42901", "carrierName": "Verizon Wireless",
        "totalStorageInMB": 11289, "totalRamInMB": 2815, "freeRamInMB": null,
        "class": "Android Large", "model": "JKM-LX1",
        "isNotificationEnabled": null, "isRooted": false, "platform": "android"
      },
      "location": {
        "countryCode": "US", "city": "Albany", "longitude": -73.7987, "latitude": 42.6664
      }
    }
  ],
  "pagination": { "page_size": 50, "current_page": 1, "next_page": 2, "has_more": true, "next_cursor": "eyJjIjoiZXlKeVpXTnZjbVJsWkc5dUlqb2lNakF5Tmk0dUxpSSI…" }
}
```

<Callout icon="📘" theme="info">
  ### Note

  Storage and RAM are reported in **MB** (`totalStorageInMB`, `totalRamInMB`, `freeRamInMB`). Timestamps are ISO-8601 zulu (`…Z`). Web sessions omit `platform` and carry `browser` / `browserVersion` instead of carrier and model fields.
</Callout>

## Analyze Sessions

This endpoint furnishes aggregated numerical data useful for statistical visualisation and analytical insights.

### Request Parameters

Send a JSON body with any of `filters`, `group_by`, `aggregation`, `comparison`, and pagination (`page`, `page_size`); the API key rides in the `X-Api-Key` header. See [Filter Operators](doc:filter-operators) for the full `group_by`, `aggregation`, and `comparison` syntax.

**Default metrics** — returned when `aggregation` is omitted:

| Metric                                   | Description                               |
| ---------------------------------------- | ----------------------------------------- |
| `session_count`                          | Total sessions                            |
| `session_unique_user_count`              | Unique users                              |
| `session_new_users_count`                | New users                                 |
| `avg_session_duration`                   | Average session length (seconds)          |
| `avg_session_gesture_count`              | Average gestures per session              |
| `avg_session_rage_gesture_count`         | Average rage gestures per session         |
| `avg_session_responsive_gesture_count`   | Average responsive gestures per session   |
| `avg_session_unresponsive_gesture_count` | Average unresponsive gestures per session |
| `avg_session_unique_screen_count`        | Average unique screens per session        |

**Group-by dimensions** — pass in `group_by` (up to two):

- **Device** — `device_model`, `device_manufacturer`, `device_class`, `device_os_version`, `device_os_name`, `device_type`, `device_platform`
- **App / SDK** — `app_version`, `sdk_version`
- **Location** — `device_country`, `device_city`
- **Browser** (web) — `browser_name`, `browser_version`
- **Time bucket** — `session_uploaded_month`, `session_uploaded_week`, `session_uploadedon_day`

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/session/analytics \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","filters":[{"attribute":"device_country","operator":"equal","value":"USA"}],"group_by":[{"attribute":"device_model","max_group_number":50}]}'
```

### Response Structure

The analytics endpoint returns aggregated session data grouped by the specified attributes, facilitating statistical charting.

```json
{
  "success": true,
  "data": [
    {
      "device_model": "SM-C7100",
      "session_count": 1577,
      "session_unique_user_count": 754,
      "session_new_users_count": 24,
      "avg_session_duration": 92.12662,
      "avg_session_gesture_count": 22.7793,
      "avg_session_rage_gesture_count": 0.2244,
      "avg_session_responsive_gesture_count": 18.5662,
      "avg_session_unresponsive_gesture_count": 4.2130,
      "avg_session_unique_screen_count": 5.6582,
      "dashboard_link": "https://app.uxcam.com/app/YOUR_APP_ID/sessions"
    }
  ],
  "pagination": { "current": 1, "next": 2, "total": 87 }
}
```

<br />
