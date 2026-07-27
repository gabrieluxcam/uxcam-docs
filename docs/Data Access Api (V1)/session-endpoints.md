---
title: Session
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

| Attribute Category | Data Type       | Attribute Name                       | Description | Usable as            |
| ------------------ | --------------- | ------------------------------------ | ----------- | -------------------- |
| DateTime           | DateTime        | `date_range`                         | ...         | Filter (date window) |
| Device             | String          | `app_version`                        | ...         | Filter · Group       |
| Device             | String          | `device_class`                       | ...         | Filter · Group       |
| Device             | String          | `device_id`                          | ...         | Filter               |
| Device             | String          | `device_manufacturer`                | ...         | Filter · Group       |
| Device             | String          | `device_model`                       | ...         | Filter · Group       |
| Device             | String          | `device_os_version`                  | ...         | Filter · Group       |
| Device             | String          | `device_platform`                    | ...         | Filter · Group       |
| Session Property   | String          | `event_name`                         | ...         | Filter               |
| Session Property   | String          | `sdk_version`                        | ...         | Filter · Group       |
| Session Property   | Bool String     | `session_crashed`                    | ...         | Filter               |
| Session Property   | Integer         | `session_duration`                   | ...         | Filter · Aggregate   |
| Session Property   | Integer         | `session_gesture_count`              | ...         | Filter · Aggregate   |
| Session Property   | Bool String     | `session_has_video`                  | ...         | Filter               |
| Session Property   | Count           | `session_new_users_count`            | ...         | **Aggregate only**   |
| Session Property   | Integer         | `session_number_of_user`             | ...         | Filter               |
| Session Property   | Integer         | `session_rage_gesture_count`         | ...         | Filter · Aggregate   |
| Session Property   | Integer         | `session_responsive_gesture_count`   | ...         | Filter · Aggregate   |
| Session Property   | Integer         | `session_screen_count`               | ...         | Filter · Aggregate   |
| Session Property   | list            | `session_screen_list`                | ...         | Filter               |
| Session Property   | Integer         | `session_unique_screen_count`        | ...         | Filter · Aggregate   |
| Session Property   | Count           | `session_unique_user_count`          | ...         | **Aggregate only**   |
| Session Property   | Integer         | `session_unresponsive_gesture_count` | ...         | Filter · Aggregate   |
| Session Property   | DateTime String | `session_uploaded_month`             | ...         | **Group only**       |
| Session Property   | DateTime String | `session_uploaded_week`              | ...         | **Group only**       |
| Session Property   | DateTime String | `session_uploadedon_day`             | ...         | **Group only**       |
| Session Property   | String          | `session_id`                         | ...         | Filter               |
| User               | String          | `device_city`                        | ...         | Filter · Group       |
| User               | String          | `device_country`                     | ...         | Filter · Group       |
| User               | String          | `uxcamuserid`                        | ...         | Filter               |
| User               | String          | `user_name`                          | ...         | Filter               |
| User               | JSON            | `user_custom_property`               | ...         | Filter               |

### Web-only Attributes

These additional attributes are available for web sessions.

| Attribute Category | Data Type | Attribute Name              | Description                                       | Usable as      |
| ------------------ | --------- | --------------------------- | ------------------------------------------------- | -------------- |
| User               | JSON      | `user_first_utm`            | The UTM of the user's first session               | Filter         |
| User               | String    | `user_first_referer`        | The referrer URL of the user's first session      | Filter         |
| User               | String    | `user_first_screen_name`    | First page the user landed on                     | Filter         |
| User               | String    | `user_first_referer_domain` | The referring domain of the user's first session  | Filter         |
| Session            | String    | `browser_name`              | User's browser name in a session                  | Filter · Group |
| Session            | String    | `browser_version`           | User's browser version in a session               | Filter · Group |
| Session            | String    | `referer`                   | URL that referred the user to the current session | Filter         |
| Session            | String    | `referer_domain`            | Domain of the referrer URL in a session           | Filter         |
| Session            | JSON      | `utm`                       | UTM parameters of the session's landing page      | Filter         |
| Session            | String    | `device_os_name`            | Device OS name from which sessions are recorded   | Filter · Group |
| Session            | String    | `device_type`               | Device type from which sessions are recorded      | Filter · Group |

> This table combines attributes usable for **filtering**, **grouping**, and **aggregation** — not every attribute supports all three. In particular, the **count** metrics (`session_new_users_count`, `session_unique_user_count`) and the upload time-buckets (`session_uploaded_month`, `session_uploaded_week`, `session_uploadedon_day`) are for `group_by`**&#x20;/&#x20;**`aggregation`**&#x20;only** — using them in `filters` returns `400`. To restrict by date, use `date_range`.

## List Sessions

This endpoint delivers qualitative data encompassing device details, user interactions, personalised properties, and location information. Sections are grouped under `property`, `user`, `location`, and the device sub-sections (`deviceBasics`, `deviceHardware`, `devicePerformance`, `deviceNetwork`, `deviceSecurity`, `deviceSettings`) — request `device` to get all of them at once.

### Request Parameters

Send a JSON body; the API key rides in the `X-Api-Key` header.

- `app_id` — required; the app to read.
- `filters` — optional filter objects; omit for the default last-30-days window.
- `show_only` — sections to return. Omit for the default `["property"]`; the example below requests all four (`property`, `user`, `device`, `location`).
- `page_size` — records per page, `1`–`2000` (default `500`).
- `cursor` — opaque cursor for the next page; omit for the first page.
- `with_video` — set `true` for a time-limited signed replay link per session.

Full field reference: [Query Parameters](doc:query-parameters-1).

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/session \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","show_only":["property","user","device","location"],"filters":[{"attribute":"device_country","operator":"equal","value":"USA"}],"page_size":500}'
```

### Response Structure

When with_video is set, sessions with a playable recording carry a video key holding a time-limited signed replay link — this covers archived replays too (a recording moved to archive storage after the retention period still gets a working link). The video key is omitted for any session without a resolvable link (no recording, or with_video not requested), so its presence always means a link is available. The lookup is best-effort — a session whose link can't be produced is returned without a video key and the request still succeeds. To query by recording existence, filter on session_has_video

```json
{
  "success": true,
  "data": [
    {
      "sessionId": "60f7dd4efd9c2f001169bb96",
      "sessionNumber": 20,
      "url": "https://tara.uxcam.com/app/YOUR_APP_ID/sessions/list/1/60f7dd4efd9c2f001169bb96",
      "video": "https://sp.uxcam.com/replay/60f7dd4efd9c2f001169bb96?signature=…",
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
        "uniqueScreens": ["HomeRoute", "ProductListRoute", "CheckOutRoute"],
        "offlineRecorded": false,
        "isCrashed": false,
        "networkType": "wifi"
      },
      "deviceBasics": {
        "osName": "O_MR1", "osVersion": "8.1", "appVersion": "1.5", "sdkVersion": "3.3.5",
        "deviceId": "03c1e123941a19ec", "type": "Phone", "language": "English", "country": "USA"
      },
      "deviceHardware": {
        "producer": "Huawei", "dpi": -1, "width": 1080, "height": 2340,
        "model": "JKM-LX1", "class": "Android Large", "platform": "android"
      },
      "devicePerformance": { "totalRamInMB": 2815, "freeRamInMB": null, "totalStorageInMB": 11289 },
      "deviceNetwork": { "carrierCode": "42901", "carrierName": "Verizon Wireless" },
      "deviceSecurity": { "isRooted": false },
      "deviceSettings": { "isNotificationEnabled": null },
      "location": {
        "countryCode": "US", "city": "Albany", "longitude": -73.7987, "latitude": 42.6664
      }
    }
  ],
  "pagination": { "page_size": 500, "current_page": 1, "next_page": 2, "has_more": true, "next_cursor": "eyJjIjoiZXlKeVpXTnZjbVJsWkc5dUlqb2lNakF5Tmk0dUxpSSI…" }
}
```

<Callout icon="📘" theme="info">
  ### Note

  Storage and RAM are reported in **MB** (`totalStorageInMB`, `totalRamInMB`, `freeRamInMB`). Timestamps are ISO-8601 zulu (`…Z`). Web sessions carry `browser` / `browserVersion` in `deviceBasics`, their `deviceHardware` omits `model` / `class` / `platform`, and the mobile-only sub-sections (`devicePerformance`, `deviceNetwork`, `deviceSecurity`, `deviceSettings`) come back as empty objects `{}`.
</Callout>

## Analyze Sessions

This endpoint furnishes aggregated numerical data useful for statistical visualisation and analytical insights.

### Request Parameters

Send a JSON body with any of `filters`, `group_by`, `aggregation`, `comparison`, and pagination (`page`, `page_size`); the API key rides in the `X-Api-Key` header. See [Filter Operators](doc:filter-operators-1) for the full `group_by`, `aggregation`, and `comparison` syntax.

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
      "dashboard_link": "https://tara.uxcam.com/app/YOUR_APP_ID/sessions"
    }
  ],
  "pagination": { "current": 1, "next": 2, "total": 87 }
}
```

### References:

&#x20;[Users](doc:users-2)<br />[Events](doc:events-2)<br />[Screen Analytics](doc:screens)<br />[Query Parameters](doc:query-parameters-1)<br />[Filter Operators](doc:filter-operators-1)<br />[Error Handling & Messages](doc:error-handling-and-messages-1)
