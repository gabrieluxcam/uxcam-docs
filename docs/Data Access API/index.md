---
title: Data Access API
excerpt: REST API for exporting session, user, and event data
deprecated: false
hidden: false
metadata:
  title: 'UXCam Data Access API'
  description: 'REST API documentation for accessing UXCam analytics data programmatically'
  robots: index
next:
  description: ''
---

# Data Access API

The UXCam Data Access API is a REST API for programmatically accessing your analytics data. Use it to export sessions, users, and events to your own systems.

<GitHubCallout type="note">**Two versions of the Data Access API.** This page documents the **classic API** (`api.uxcam.com/v2/...`): `GET` requests with `appid` and `apikey` as query parameters. A newer **v1 API** for the new UXCam dashboard (`api.uxcam.com/api/data-access/v1/...`) uses `POST` with a JSON body and the API key in an `X-Api-Key` header — see [Data Access API v1](/docs/data-access-api-1). Both versions use the same Data Access API key. The classic API remains supported; existing integrations don't need to change.</GitHubCallout>

<GitHubCallout type="note">This is different from the SDK APIs. The Data Access API is a server-side REST API for exporting data. For mobile/web SDK methods, see the [SDK Reference](/docs/sdk-reference).</GitHubCallout>

---

## Quick Start

### Try It in Postman

<HTMLBlock>{`
<a href="https://web.postman.co/network/import?collection=9127779-b44a835e-e256-41ba-862d-3b10388c7b67-2s935it5r2" target="_blank" rel="noopener noreferrer">
  <img src="https://run.pstmn.io/button.svg" alt="Run in Postman">
</a>
`}</HTMLBlock>

[View Postman Documentation](https://documenter.getpostman.com/view/9127779/2s935it5r2)

---

## Authentication

All API requests require two authentication parameters, sent as **query parameters** on every request:

| Query parameter | Description | Where to Find |
|-----------|-------------|---------------|
| `appid` | Your app's unique identifier (App ID) | Dashboard > App Settings > Application |
| `apikey` | Authentication key for API access (API Key) | Dashboard > App Settings > Data Access API |

<GitHubCallout type="warning">Pass `appid` and `apikey` in the query string, not as request headers. Requests that send the credentials as headers (for example `X-App-Id` / `X-Api-Key`) are rejected with `401 Request authentication failed`.</GitHubCallout>

### Getting Your Credentials

1. Log in to your [UXCam Dashboard](https://app.uxcam.com)
2. Go to **App Settings**
3. Select the **Application** tab to find your App ID
4. Select the **Data Access API** tab
5. Generate or copy your API Key

---

## API Endpoints

<Cards columns={2}>
  <Card title="Sessions" href="/docs/sessions" icon="fa-solid fa-video">
    Query and export session data
  </Card>

  <Card title="Users" href="/docs/users" icon="fa-solid fa-users">
    Query and export user data
  </Card>

  <Card title="Events" href="/docs/events-endpoint" icon="fa-solid fa-bolt">
    Query and export event data
  </Card>

  <Card title="Query Parameters" href="/docs/api-query-parameters" icon="fa-solid fa-filter">
    Filtering and pagination options
  </Card>

  <Card title="Data Deletion" href="/docs/data-deletion-api" icon="fa-solid fa-trash">
    Erase users and sessions for GDPR/CCPA requests
  </Card>
</Cards>

---

## Base URL

```
https://api.uxcam.com/v2/
```

---

## Request Format

All data endpoints (`session`, `user`, `event` and their `/analytics` variants) use:
- **Method**: `GET` (`POST` returns `405 Method Not Allowed`)
- **Parameters**: query string (URL-encoded); there is no JSON request body
- **Authentication**: `appid` and `apikey` query parameters

### Example Request

```bash
# Sessions uploaded between Jan 1 and Jan 31, 2024, 100 per page
curl "https://api.uxcam.com/v2/session" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'filters=[{"attribute":"date_range","operator":"between_dates","value":{"lower":"2024-01-01","upper":"2024-01-31"}}]' \
  --data-urlencode 'page=1' \
  --data-urlencode 'page_size=100'
```

---

## Response Format

All responses return JSON:

```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "current": 1,
    "next": 2,
    "total": 100
  }
}
```

`pagination.total` is the number of items in the current page, not the overall result count. Keep requesting `page = next` until `next` is `null`.

---

## Reference Documentation

| Topic | Description |
|-------|-------------|
| [Error Handling](/docs/error-handling-and-messages) | HTTP status codes and error messages |
| [Query Parameters](/docs/api-query-parameters) | Filtering, sorting, and pagination |
| [Filter Operators](/docs/filter-operators) | Advanced filtering syntax |

---

## Use Cases

### Export to Data Warehouse

Pull session data into your analytics infrastructure:

```python
import json
import requests

def export_sessions(start_date, end_date):
    sessions, page = [], 1
    date_filter = [{
        "attribute": "date_range",
        "operator": "between_dates",
        "value": {"lower": start_date, "upper": end_date},
    }]
    while page:
        response = requests.get(
            "https://api.uxcam.com/v2/session",
            params={
                "appid": "YOUR_APP_ID",
                "apikey": "YOUR_API_KEY",
                "filters": json.dumps(date_filter),
                "page": page,
                "page_size": 500,
            },
        )
        body = response.json()
        sessions.extend(body.get("data") or [])
        page = body.get("pagination", {}).get("next")
    return sessions
```

### User Deletion (GDPR)

Erasure is a separate API with its own key. Submit users or sessions to `POST /v2/deletion`; see the [Data Deletion API](/docs/data-deletion-api).

### Session Lookup

Find sessions for a specific user:

```bash
curl "https://api.uxcam.com/v2/session" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'filters=[{"attribute":"uxcamuserid","operator":"equal","value":"60f7dd46972a633e88696d6b"}]'
```

---

## Rate Limits

| Tier | Requests per Minute |
|------|---------------------|
| Standard | 60 |
| Enterprise | Custom |

Exceeding rate limits returns `429 Too Many Requests`.

---

## See Also

- [Data Deletion API](/docs/data-deletion-api) - Erase users and sessions programmatically
- [SDK Reference](/docs/sdk-reference) - Mobile/Web SDK methods
- [Privacy and Compliance](/docs/privacy-and-compliance) - GDPR/CCPA handling
