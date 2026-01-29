---
title: Users Endpoint
excerpt: Query and export user data via REST API
deprecated: false
hidden: false
metadata:
  title: 'Users API - UXCam Data Access'
  description: 'REST API for querying and exporting UXCam user data'
  robots: index
next:
  description: ''
---

# Users Endpoint

Query and export user data from your UXCam account.

## Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /v2/user` | List users (qualitative data) |
| `GET /v2/user/analytics` | Aggregate user analytics (quantitative data) |

---

## List Users

Returns detailed user data including device info, engagement metrics, and custom properties.

### Request

```bash
curl "https://api.uxcam.com/v2/user" \
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
      "uxcamUserId": "60f7e1a43d4dcd370b7e0b02",
      "userId": "user_123",
      "url": "https://app.uxcam.com/app/.../users/...",
      "property": {
        "alias": "Anonymous User",
        "kUXCam_UserIdentity": "user_123"
      },
      "usage": {
        "registeredOn": "2022-11-12T06:48:43Z",
        "lastseenOn": "2023-01-11T06:27:48Z",
        "totaluser": 12,
        "totaluserTime": 475.85,
        "totalGesture": 152,
        "screenCount": 92
      },
      "location": {
        "country": "USA",
        "city": "New York"
      },
      "device": {
        "model": "SM-A525F",
        "platform": "android"
      }
    }
  ]
}
```

---

## User Analytics

Returns aggregated statistics for users matching your query.

### Request

```bash
curl "https://api.uxcam.com/v2/user/analytics" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY" \
  -G \
  --data-urlencode 'filters=[{"attribute":"device_country","operator":"equal","value":"USA"}]' \
  --data-urlencode 'group_by=[{"attribute":"device_manufacturer","max_group_number":50}]'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "user_count": 2993,
      "session_new_users_count": 173,
      "avg_user_session_count": 16.68,
      "avg_user_session_duration": 1402.01
    }
  ]
}
```

---

## User Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `uxcamuserid` | String | UXCam's internal user ID |
| `user_name` | String | Random alias (not real name) |
| `user_first_seen_on` | DateTime | First session timestamp |
| `user_session_duration` | Integer | Total time in app (seconds) |
| `user_gesture_count` | Integer | Total interactions |
| `user_rage_gesture_count` | Integer | Total rage taps |
| `user_custom_property` | JSON | Your custom properties |
| `device_country` | String | Country from IP |
| `device_platform` | String | "android" or "ios" |

For the complete attribute list, see the [full Users documentation](/docs/users-1).

---

## See Also

- [Query Parameters](query-parameters) - Filtering and pagination
- [Filter Operators](filter-operators) - Advanced filter syntax
- [Sessions Endpoint](sessions) - Query session data
- [Events Endpoint](events) - Query event data
