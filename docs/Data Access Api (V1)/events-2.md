---
title: 'Events '
deprecated: false
hidden: true
metadata:
  robots: index
---
**Events** capture what users do inside a session — custom events you log from the SDK (e.g. `Signup_Completed`, `purchased`) as well as automatic events such as `Rage Tap` and `UI Freeze`. The Events endpoints return individual event records and aggregated event analytics.

<Callout icon="📘" theme="info">
  ### Note

  Looking for how to _send_ events from your app? See the SDK _Send Custom Events_ guide. This page documents how to _read_ events back through the Data Access API.
</Callout>

## Endpoints

`POST /api/data-access/v1/event`

`POST /api/data-access/v1/event/analytics`

## Filtering events

The event list has a focused, fail-closed filter surface — a date window plus the attributes below. Anything else returns `400`. Scalar attributes match by equality; `event_name` also accepts a list (`in`).

| Attribute                                                                                    | Operator(s)    | Value                                              |
| -------------------------------------------------------------------------------------------- | -------------- | -------------------------------------------------- |
| `event_name`                                                                                 | `equal` / `in` | One name or a list of names                        |
| `device_model`, `device_manufacturer`, `os_version`, `app_version`, `country`, `screen_name` | `equal`        | A single string                                    |
| `device_platform`                                                                            | `equal`        | `1` = Android, `2` = iOS, `3` = Web                |
| `has_video`                                                                                  | `equal`        | Boolean                                            |
| `event_custom_property`                                                                      | `equal`        | With `property_name` + value (one property filter) |
| `date_range`                                                                                 | date operators | See [Filter Operators](doc:filter-operators-1)     |

## Sections

An event record groups into four sections. Omitting `show_only` returns the default set `["eventProperty", "sessionProperty"]` (the event's data plus its parent session — including `sessionId`, the join key) — pass `show_only` to request more:

| Section           | Contents                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `sessionProperty` | The parent session: `sessionId`, `durationSec`, `totalScreen`, `uniqueScreensCount`, `networkType`, `sessionNumber`, `isCrashed`, `country`. |
| `userProperty`    | The user's id (`uxcamuserid`, `kUXCam_UserIdentity`) plus custom user properties.                                                            |
| `deviceBasics`    | `deviceId`, `appVersion`, `osVersion`.                                                                                                       |
| `deviceHardware`  | `model`, `producer`, `class`, `platform`. Request `device` to get both device sub-sections.                                                  |
| `eventProperty`   | The custom properties attached to this event.                                                                                                |

## List events

`POST /api/data-access/v1/event`

### Request

Send a JSON body; the API key rides in the `X-Api-Key` header.

- `app_id` — required; the app to read.
- `filters` — optional; the focused surface above, plus a `date_range` window (omit for the last 30 days).
- `show_only` — sections to return. Omit for the default `["eventProperty", "sessionProperty"]`; the example below requests all four.
- `page_size` — records per page, `1`–`2000` (default `500`).
- `cursor` — opaque cursor for the next page; omit for the first page.

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/event \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","show_only":["sessionProperty","userProperty","device","eventProperty"],"filters":[{"attribute":"event_name","operator":"in","value":["purchased"]}],"page_size":500}'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "eventId": "651a2f4c9b1e4a0012ab34cd",
      "eventName": "purchased",
      "eventScreen": "CheckOutRoute",
      "eventDate": "2026-06-28T14:12:07Z",
      "eventPropertyTime": 3.42,
      "url": "https://tara.uxcam.com/app/YOUR_APP_ID/sessions/list/1/60f7dd4efd9c2f001169bb96",
      "sessionProperty": {
        "sessionId": "60f7dd4efd9c2f001169bb96", "isCrashed": false,
        "durationSec": 55.009, "totalGesture": 26, "totalScreen": 17,
        "uniqueScreensCount": 5, "networkType": "wifi", "sessionNumber": 20, "country": "USA"
      },
      "userProperty": {
        "uxcamuserid": "60f7dd46972a633e88696d6b", "kUXCam_UserIdentity": "U#5066",
        "loyalty_card": "no"
      },
      "deviceBasics": {
        "deviceId": "03c1e123941a19ec", "appVersion": "1.5", "osVersion": "8.1"
      },
      "deviceHardware": {
        "model": "JKM-LX1", "producer": "Huawei", "class": "Android Large", "platform": "android"
      },
      "eventProperty": { "plan": "pro", "price_cents": "1499" }
    }
  ],
  "pagination": { "page_size": 500, "current_page": 1, "next_page": 2, "has_more": true, "next_cursor": "eyJjIjoiZXlKMWNHeHZZV1JsWkc5dUlqb2lNakF5TmkuLi4i…" }
}
```

<Callout icon="📘" theme="info">
  ### Note

  Event `device.platform` is the label (`"android"` / `"ios"`), consistent with the Sessions and Users endpoints. (The `device_platform` **filter** input still uses the numeric codes `1`/`2`/`3`.) Custom event-property values are returned as strings (e.g. `"1499"`).
</Callout>

## Analyze events

`POST /api/data-access/v1/event/analytics`

Returns event counts, optionally grouped by the dimension(s) you pass in `group_by`. Omit `group_by` for an ungrouped total across all events (the example below groups by `event_name`). Event analytics are count-based (no averages).

### Default metrics

| Metric                       | Description                          |
| ---------------------------- | ------------------------------------ |
| `event_count`                | Total events                         |
| `event_unique_user_count`    | Unique users who triggered the event |
| `event_unique_session_count` | Unique sessions containing the event |

### Group-by dimensions

Pass in `group_by` (up to two):

- **Event** — `event_name`, `event_screen_name`
- **Device** — `device_model`, `device_manufacturer`, `device_class`, `device_os_name`, `device_os_version`, `device_type`, `device_platform`
- **App** — `app_version`
- **Browser** (web) — `browser_name`, `browser_version`
- **Time bucket** — `event_uploaded_month`, `event_uploaded_week`, `event_uploadedon_day`

### Request

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/event/analytics \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","group_by":[{"attribute":"event_name","max_group_number":50}]}'
```

### Response

```json
{
  "success": true,
  "data": [
    { "event_name": "rageTap", "event_count": 5900000,
      "event_unique_user_count": 210334, "event_unique_session_count": 480221,
      "dashboard_link": "https://tara.uxcam.com/app/YOUR_APP_ID/sessions" },
    { "event_name": "purchased", "event_count": 463000,
      "event_unique_user_count": 51002, "event_unique_session_count": 88771,
      "dashboard_link": "https://tara.uxcam.com/app/YOUR_APP_ID/sessions" }
  ],
  "pagination": { "current": 1, "next": null, "total": 32 }
}
```

<br />
