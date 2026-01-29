---
title: Events Endpoint
excerpt: Query and export event data via REST API
deprecated: false
hidden: false
metadata:
  title: 'Events API - UXCam Data Access'
  description: 'REST API for querying and exporting UXCam event data'
  robots: index
next:
  description: ''
---

# Events Endpoint

Query and export custom event data from your UXCam account.

## Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /v2/event` | List events (qualitative data) |
| `GET /v2/event/analytics` | Aggregate event analytics (quantitative data) |

---

## List Events

Returns detailed event data including session context, user properties, and custom event properties.

### Request

```bash
curl "https://api.uxcam.com/v2/event" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY" \
  -G \
  --data-urlencode 'page=1' \
  --data-urlencode 'page_size=50' \
  --data-urlencode 'filters=[{"attribute":"event_name","operator":"equal","value":"purchase"}]'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "eventId": "9d0cb696-65e2-4783-9556-352bfd71009f",
      "eventName": "purchase",
      "eventScreen": "CheckoutActivity",
      "eventDate": "2023-01-22T17:31:57Z",
      "url": "https://app.uxcam.com/app/.../sessions/...",
      "sessionProperty": {
        "sessionId": "60f7dd4d46c872001188eb3f",
        "hasVideo": true,
        "durationSec": 98.77
      },
      "userProperty": {
        "kUXCam_UserIdentity": "user_123",
        "subscription": "premium"
      },
      "eventProperty": {
        "amount": 29.99,
        "currency": "USD"
      },
      "device": {
        "model": "SM-A525F",
        "platform": 1
      }
    }
  ]
}
```

---

## Event Analytics

Returns aggregated statistics for events matching your query.

### Request

```bash
curl "https://api.uxcam.com/v2/event/analytics" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY" \
  -G \
  --data-urlencode 'filters=[{"attribute":"event_name","operator":"equal","value":"purchase"}]' \
  --data-urlencode 'group_by=[{"attribute":"event_uploadedon_day"}]'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "event_count": 165159,
      "event_unique_user_count": 2993,
      "event_unique_session_count": 12137
    }
  ]
}
```

---

## Event Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `event_name` | String | Name of the event |
| `event_screen_name` | String | Screen where event occurred |
| `event_custom_property` | JSON | Properties sent with event |
| `event_uploadedon_day` | DateTime | Day event was recorded |
| `device_platform` | String | "android" or "ios" |
| `session_id` | String | Session containing event |
| `uxcamuserid` | String | User who triggered event |

For the complete attribute list, see the [full Events documentation](/docs/events).

---

## Use Cases

### Funnel Analysis

Count users who completed each step:

```bash
# Step 1: Viewed product
curl "https://api.uxcam.com/v2/event/analytics?..." \
  --data-urlencode 'filters=[{"attribute":"event_name","operator":"equal","value":"view_product"}]'

# Step 2: Added to cart
curl "https://api.uxcam.com/v2/event/analytics?..." \
  --data-urlencode 'filters=[{"attribute":"event_name","operator":"equal","value":"add_to_cart"}]'

# Step 3: Purchased
curl "https://api.uxcam.com/v2/event/analytics?..." \
  --data-urlencode 'filters=[{"attribute":"event_name","operator":"equal","value":"purchase"}]'
```

---

## See Also

- [Query Parameters](query-parameters) - Filtering and pagination
- [Filter Operators](filter-operators) - Advanced filter syntax
- [Sessions Endpoint](sessions) - Query session data
- [Users Endpoint](users) - Query user data
