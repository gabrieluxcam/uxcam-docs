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

All API requests require two authentication parameters:

| Parameter | Description | Where to Find |
|-----------|-------------|---------------|
| `App ID` | Your app's unique identifier | Dashboard > App Settings > Application |
| `API Key` | Authentication key for API access | Dashboard > App Settings > Data Access API |

### Getting Your Credentials

1. Log in to your [UXCam Dashboard](https://app.uxcam.com)
2. Go to **App Settings**
3. Select the **Application** tab to find your App ID
4. Select the **Data Access API** tab
5. Generate or copy your API Key

---

## API Endpoints

<Cards columns={2}>
  <Card title="Sessions" href="sessions" icon="fa-solid fa-video">
    Query and export session data
  </Card>

  <Card title="Users" href="users" icon="fa-solid fa-users">
    Query and export user data
  </Card>

  <Card title="Events" href="events-endpoint" icon="fa-solid fa-bolt">
    Query and export event data
  </Card>

  <Card title="Query Parameters" href="api-query-parameters" icon="fa-solid fa-filter">
    Filtering and pagination options
  </Card>
</Cards>

---

## Base URL

```
https://api.uxcam.com/v2/
```

---

## Request Format

All requests use:
- **Method**: GET or POST (endpoint-specific)
- **Content-Type**: `application/json`
- **Authentication**: Headers with App ID and API Key

### Example Request

```bash
curl -X GET "https://api.uxcam.com/v2/sessions" \
  -H "Content-Type: application/json" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY" \
  -d '{
    "startDate": "2024-01-01",
    "endDate": "2024-01-31",
    "limit": 100
  }'
```

---

## Response Format

All responses return JSON:

```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "total": 1234,
    "limit": 100,
    "offset": 0
  }
}
```

---

## Reference Documentation

| Topic | Description |
|-------|-------------|
| [Error Handling](error-handling-and-messages) | HTTP status codes and error messages |
| [Query Parameters](api-query-parameters) | Filtering, sorting, and pagination |
| [Filter Operators](filter-operators) | Advanced filtering syntax |

---

## Use Cases

### Export to Data Warehouse

Pull session data into your analytics infrastructure:

```python
import requests

def export_sessions(start_date, end_date):
    response = requests.get(
        "https://api.uxcam.com/v2/sessions",
        headers={
            "X-App-Id": "YOUR_APP_ID",
            "X-Api-Key": "YOUR_API_KEY"
        },
        json={
            "startDate": start_date,
            "endDate": end_date,
            "limit": 1000
        }
    )
    return response.json()["data"]
```

### User Deletion (GDPR)

Delete user data for compliance requests:

```bash
curl -X DELETE "https://api.uxcam.com/v2/users/user_12345" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY"
```

### Session Lookup

Find sessions for a specific user:

```bash
curl -X GET "https://api.uxcam.com/v2/sessions" \
  -H "Content-Type: application/json" \
  -H "X-App-Id: YOUR_APP_ID" \
  -H "X-Api-Key: YOUR_API_KEY" \
  -d '{
    "filters": {
      "userId": "user_12345"
    }
  }'
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

- [SDK Reference](/docs/sdk-reference) - Mobile/Web SDK methods
- [Privacy and Compliance](/docs/privacy-and-compliance) - GDPR/CCPA handling
