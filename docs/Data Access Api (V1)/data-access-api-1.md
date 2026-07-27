---
title: Data Access API
deprecated: false
hidden: true
metadata:
  robots: index
next:
  pages:
    - slug: data-access-api-1
      title: Data Access API
      type: basic
---
The UXCam **Data Access API** is built on REST principles with predictable, resource-oriented URLs. Every endpoint is a `POST` that accepts a JSON request body, returns JSON, and uses standard HTTP status codes. Authentication is handled with a single header, so no credentials ever appear in a URL.

### Go ahead and try out our REST APIs with credentials on Postman)


[<Image src="https://run.pstmn.io/button.svg" alt="Run in Postman" align="left" wrap={true} />](https://documenter.getpostman.com/view/29342606/2sBY4JxP93)


<br />

# Postman Guide

Postman enables you to test and utilize UXCam's Data Access APIs without requiring a developer account. You can set up and send requests and receive responses for the provided Data Access APIs.

### Steps to set up postman for UXCAM's Data Access APIs collection:)

1. Log in to your Postman account
2. Create your own fork of the collection by clicking the “Click here” button below
3. Give a name to your fork and select a workspace.

### After importing, set these collection variables:

| Variable   | Value                    |
| ---------- | ------------------------ |
| `base_url` | `https://tara.uxcam.com` |
| `app_id`   | Your app's `app_id`      |
| `api_key`  | Your Data Access API key |

## Base URL

All endpoints share a single base URL:

[https://tara.uxcam.com](https://tara.uxcam.com)

Each resource has a list endpoint and an analytics endpoint. Lists return individual records (qualitative data); analytics endpoints return aggregated, grouped numbers suited to charts and dashboards.

| Resource | List endpoint                      | Analytics endpoint                           |
| -------- | ---------------------------------- | -------------------------------------------- |
| Sessions | `POST /api/data-access/v1/session` | `POST /api/data-access/v1/session/analytics` |
| Users    | `POST /api/data-access/v1/user`    | `POST /api/data-access/v1/user/analytics`    |
| Events   | `POST /api/data-access/v1/event`   | `POST /api/data-access/v1/event/analytics`   |
| Screens  |                                    | `POST /api/data-access/v1/screen/analytics`  |

## Authentication

Every request needs two things — the application it targets and a secret key that proves you may read that app's data:

| Parameter | Where it goes      | Description                                                                                    |
| --------- | ------------------ | ---------------------------------------------------------------------------------------------- |
| `app_id`  | JSON request body  | The application identifier whose data you want to read.                                        |
| API key   | `X-Api-Key` header | Your Data Access API key. Kept in a header so it never lands in URLs, proxies, or server logs. |

<Callout icon="🚧" theme="warn">
  ### Important

  The API key is a secret. Send it only in the `X-Api-Key` header, never in the URL or query string. Requests missing either the header or `app_id` are rejected with `401 MISSING_CREDENTIALS`.
</Callout>

### Get your authentication parameters

1. Log in to your UXCam **Dashboard**.
2. Open **App Settings** for the app you want to query.
3. Select the **Data Access API** tab.
4. Click **Generate API key** (or copy the existing one). Your `app_id` is shown on the same settings screen and in the dashboard URL for the app.

<Callout icon="👍" theme="okay">
  ### Success

  **You're all set.** With your `app_id` and API key you can make your first request below.
</Callout>

## Make your first request

List the most recent sessions for your app. The smallest valid body is just `app_id` plus a `page_size`; with no filters, the API returns the last 30 days of data. Omitting `show_only` returns each endpoint's lean **default section set** (`/session`: `property` · `/user`: `usage` · `/event`: `eventProperty` + `sessionProperty`); pass `show_only` to request more — the examples below request all sections. See [Query Parameters](doc:query-parameters-1) for the full section reference.

```curl
curl -X POST https://tara.uxcam.com/api/data-access/v1/session \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","show_only":["property","user","device","location"],"page_size":500}'
```

```javascript
fetch("https://tara.uxcam.com/api/data-access/v1/session", {
  method: "POST",
  headers: {
    "X-Api-Key": "YOUR_API_KEY",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ app_id: "YOUR_APP_ID", show_only: ["property", "user", "device", "location"], page_size: 500 }),
});
```

```python
import requests

requests.post(
    "https://tara.uxcam.com/api/data-access/v1/session",
    headers={"X-Api-Key": "YOUR_API_KEY"},
    json={"app_id": "YOUR_APP_ID", "show_only": ["property", "user", "device", "location"], "page_size": 500},
)
```

## Response envelope

Every successful response uses the same top-level shape:

```json
{
  "success": true,
  "data": [ /* records or grouped rows */ ],
  "pagination": { /* see below */ }
}
```

### Pagination

**List endpoints** (`/session`, `/user`, `/event`) use _cursor_ pagination: read `pagination.next_cursor` from a response and pass it back as `cursor` in the next request to fetch the following page. When `has_more` is `false` (`next_cursor` is `null`), you've reached the end. `current_page`/`next_page` count where you are in the walk — they're informational only; you can't jump to a page number, the cursor is the only way forward.

```json
"pagination": {
  "page_size": 500,
  "current_page": 1,
  "next_page": 2,
  "has_more": true,
  "next_cursor": "eyJjIjoiZXlKeVpXTnZjbVJsWkc5dUlqb2lNakF5Tmkwd05pMHlPRlF4TkRveE1qb3dOMW9pTEEi…"
}
```

**Analytics endpoints** return a bounded set of grouped rows and use simple offset pagination instead:

```json
"pagination": {
  "current": 1,
  "next": 2,
  "total": 87
}
```

<Callout icon="📘" theme="info">
  ### Opaque cursor

  Treat `next_cursor` as an **opaque** token — pass it back exactly as received. Do not parse or construct it yourself; a malformed cursor returns `400 INVALID_REQUEST`.
</Callout>

## Rate limits

Limits are enforced per `app_id`:

- Up to **5 requests per second**
- Up to **500 requests per hour**
- At most **2000 records per request** (`page_size` cap)

Authenticated responses — a successful `200`, and a `429` when a limit is hit — return `X-RateLimit-Limit`, `X-RateLimit-Remaining`, and `X-RateLimit-Reset` headers so you can pace requests; a `429` also carries a `Retry-After` header and code `RATE_LIMITED`. Requests rejected before authentication (e.g. `401` for a missing/invalid key, `415` for a wrong content type) carry no rate-limit headers, because no key has been metered yet. See [Error Handling & Messages](doc:error-handling-and-messages) for details.

## Response compression

Large responses are gzip-compressed to save bandwidth. When your request sends
`Accept-Encoding: gzip` (most HTTP clients do automatically) and the response body
is **2 MB or larger**, the API returns it gzip-compressed with:

- `Content-Encoding: gzip`
- `Vary: Accept-Encoding`

Smaller responses are returned uncompressed. Standard clients decompress
transparently — `curl --compressed`, browsers, Python `requests`, and `fetch`
all handle it with no code change. Only the Data Access API endpoints compress.
⚠️ Rate limits section itself is unchanged — the 2000 records per request cap still holds. Do not edit that number.

<br />
