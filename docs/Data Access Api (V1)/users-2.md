---
title: Users
deprecated: false
hidden: true
metadata:
  robots: index
---
A **user** is an individual who engages with your app, identified by a stable UXCam user id. The Users endpoints return per-user detail and aggregated per-user analytics.

## Endpoints

`POST /api/data-access/v1/user`

`POST /api/data-access/v1/user/analytics`

## User Data Attributes

Users support the **same filter attributes as [Sessions](doc:session-endpoints)** — device, location, app / SDK, session properties, time buckets, custom properties, and the web-only attributes — matched against each user's sessions (device, location, and app attributes match the user's latest snapshot). In addition, the Users endpoint provides two user-specific date attributes:

| Data Type | Attribute Name       | Description                                                    |
| --------- | -------------------- | -------------------------------------------------------------- |
| DateTime  | `user_first_seen_on` | The user's earliest session date within the selected window    |
| DateTime  | `user_last_seen_on`  | The user's most recent session date within the selected window |

## Sections

A user record is grouped into four sections. Omitting `show_only` returns the default set `["usage"]` — pass it to request more:

| Section    | Contents                                                                                                                                                   |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `property` | Custom user properties. For web users, also first-touch attribution (`initialLandingUTM`, `initialReferer`, `initialRefererDomain`, `initialLandingPage`). |
| `usage`    | Per-user aggregates for the queried window, plus `registeredOn` / `lastseenOn` and an all-time `totalLifetime` block.                                      |
| `location` | `countryCode`, `city`, `country`.                                                                                                                          |
| `device`   | Latest device snapshot — mobile: `model`, `platform`; web: `os`, `browser`, `browserVersion`, `type`.                                                      |

<Callout icon="📘" theme="info">
  ### Note

  Top-level `usage` counts are **scoped to the queried window** (default: last 30 days). The nested `totalLifetime` block carries all-time cumulative totals, so an ETL job can keep running totals without overwriting them from a single window.
</Callout>

## List users

`POST /api/data-access/v1/user`

### Request

Send a JSON body; the API key rides in the `X-Api-Key` header.

- `app_id` — required; the app to read.
- `filters` — optional filter objects; omit for the default last-30-days window.
- `show_only` — sections to return. Omit for the default `["usage"]`; the example below requests all four (`property`, `usage`, `location`, `device`).
- `page_size` — records per page, `1`–`2000` (default `50`).
- `cursor` — opaque cursor for the next page; omit for the first page.

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/user \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","show_only":["property","usage","location","device"],"filters":[{"attribute":"device_country","operator":"equal","value":"USA"}],"page_size":50}'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "uxcamUserId": "60f7dd46972a633e88696d6b",
      "userId": "U#5066",
      "url": "https://tara.uxcam.com/app/YOUR_APP_ID/users/60f7dd46972a633e88696d6b",
      "property": {
        "userProperty": { "gender": "male", "age_group": "10-19", "loyalty_card": "no" }
      },
      "usage": {
        "registeredOn": "2026-05-21T08:39:42Z",
        "lastseenOn": "2026-06-28T10:43:47Z",
        "totalSession": 12,
        "totalSessionTime": 640.51,
        "totalGesture": 214,
        "screenCount": 96,
        "eventCount": 18,
        "rageGestureCount": 2,
        "responsiveGestureCount": 180,
        "unresponsiveGestureCount": 34,
        "totalLifetime": {
          "totalSession": 41, "totalSessionTime": 2210.88, "totalGesture": 903,
          "screenCount": 402, "eventCount": 76, "rageGestureCount": 7
        }
      },
      "device": {
        "deviceId": "03c1e123941a19ec", "appVersion": "1.5", "osVersion": "8.1",
        "model": "JKM-LX1", "platform": "android"
      },
      "location": { "countryCode": "US", "city": "Albany", "country": "USA" }
    }
  ],
  "pagination": { "page_size": 50, "current_page": 1, "next_page": 2, "has_more": true, "next_cursor": "eyJjIjoiZXlKc1lYTjBYM05sWlc0aU9pSXlNREkyLi4uIg…" }
}
```

## Analyze users

`POST /api/data-access/v1/user/analytics`

Returns aggregated per-user numbers, commonly grouped by `device_platform`. Omit `aggregation` for the default metric set.

### Default metrics

| Metric                          | Description                                   |
| ------------------------------- | --------------------------------------------- |
| `user_count`                    | Total users                                   |
| `session_new_users_count`       | New users                                     |
| `avg_user_session_count`        | Average sessions per user                     |
| `avg_user_session_duration`     | Average total session time per user (seconds) |
| `avg_user_rage_gesture_count`   | Average rage gestures per user                |
| `avg_user_event_count`          | Average events per user                       |
| `avg_user_session_screen_count` | Average screens per user                      |

### Supported aggregations

| Attribute                                                                  | Operators |
| -------------------------------------------------------------------------- | --------- |
| `user_count`, `session_new_users_count`                                    | `count`   |
| `user_session_count`, `user_session_duration`                              | `avg`     |
| `user_rage_gesture_count`, `user_event_count`, `user_session_screen_count` | `avg`     |

**Group-by dimensions** — the same set as [Sessions analytics](doc:session-endpoints): Device, App / SDK, Location, Browser, and Time-bucket dimensions.

### Request

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/user/analytics \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","group_by":[{"attribute":"device_platform"}]}'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "device_platform": "android",
      "user_count": 18402,
      "session_new_users_count": 1204,
      "avg_user_session_count": 3.41,
      "avg_user_session_duration": 74.22,
      "avg_user_rage_gesture_count": 0.19,
      "avg_user_event_count": 2.7,
      "avg_user_session_screen_count": 6.1,
      "dashboard_link": "https://tara.uxcam.com/app/YOUR_APP_ID/sessions"
    }
  ],
  "pagination": { "current": 1, "next": null, "total": 3 }
}
```

<br />