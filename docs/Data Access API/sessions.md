---
title: Sessions Endpoint
excerpt: Query and export session data via REST API
deprecated: false
hidden: false
metadata:
  title: 'Sessions API - UXCam Data Access'
  description: 'REST API for querying and exporting UXCam session data'
  robots: index
next:
  description: ''
---

# Sessions Endpoint

Query and export session data from your UXCam account.

## Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /v2/session` | List sessions (qualitative data) |
| `GET /v2/session/analytics` | Aggregate session analytics (quantitative data) |

---

## List Sessions

Returns detailed session data including device info, user properties, and interactions.

### Request

```bash
curl "https://api.uxcam.com/v2/session" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY" \
  -G \
  --data-urlencode 'page=1' \
  --data-urlencode 'page_size=50' \
  --data-urlencode 'filters=[{"attribute":"device_country","operator":"equal","value":"USA"}]'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "sessionId": "60f7dd4efd9c2f001169bb96",
      "video": "https://...",
      "sessionNumber": 20,
      "url": "https://app.uxcam.com/app/.../sessions/...",
      "property": {
        "uploadedServerTime": "2023-01-16 10:43:47",
        "hasVideo": true,
        "isCrashed": false,
        "durationSec": 55.009,
        "totalGesture": 26
      },
      "user": {
        "kUXCam_UserIdentity": "user_123",
        "uxcamuserid": "60f7dd46972a633e88696d6b"
      },
      "device": {
        "platform": "android",
        "model": "SM-A525F",
        "osVersion": "13"
      },
      "location": {
        "country": "USA",
        "city": "New York"
      }
    }
  ]
}
```

---

## Session Analytics

Returns aggregated statistics for sessions matching your query.

### Request

```bash
curl "https://api.uxcam.com/v2/session/analytics" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY" \
  -G \
  --data-urlencode 'filters=[{"attribute":"device_country","operator":"equal","value":"USA"}]' \
  --data-urlencode 'group_by=[{"attribute":"device_model","max_group_number":50}]'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "device_model": "SM-A525F",
      "session_count": 1577,
      "session_unique_user_count": 754,
      "avg_session_duration": 92.12,
      "avg_session_gesture_count": 22.78
    }
  ]
}
```

---

## Session Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `session_id` | String | Unique session identifier |
| `session_duration` | Integer | Session length in seconds |
| `session_crashed` | Boolean | Whether session ended in crash |
| `session_has_video` | Boolean | Whether video is available |
| `session_gesture_count` | Integer | Total user interactions |
| `session_rage_gesture_count` | Integer | Rage taps detected |
| `session_screen_count` | Integer | Screens visited |
| `device_platform` | String | "android" or "ios" |
| `device_model` | String | Device model name |
| `device_country` | String | Country from IP |
| `app_version` | String | App version |

For the complete attribute list, see the [full Sessions documentation](/docs/sessions-1).

---

## See Also

- [Query Parameters](query-parameters) - Filtering and pagination
- [Filter Operators](filter-operators) - Advanced filter syntax
- [Users Endpoint](users) - Query user data
- [Events Endpoint](events) - Query event data
