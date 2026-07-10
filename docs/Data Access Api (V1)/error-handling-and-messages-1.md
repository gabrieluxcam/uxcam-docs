---
title: Error Handling and Messages
deprecated: false
hidden: true
metadata:
  robots: index
---
The API returns a standard HTTP status code with every response. Successful reads are `200`; problems carry a JSON error body describing what went wrong.

## Status codes

| Status                  | Reason                 | Description                                                                                                     |
| ----------------------- | ---------------------- | --------------------------------------------------------------------------------------------------------------- |
| `200`                   | OK                     | The request succeeded.                                                                                          |
| `400`                   | Bad Request            | The request was malformed — e.g. an invalid cursor, an unknown filter attribute, or an unsupported aggregation. |
| `401`                   | Unauthorized           | Missing or invalid credentials — no `app_id`/`X-Api-Key`, or the key doesn't match the app.                     |
| `404`                   | Not Found              | The requested resource doesn't exist.                                                                           |
| `415`                   | Unsupported Media Type | The request wasn't sent as `application/json`.                                                                  |
| `422`                   | Unprocessable Entity   | The body failed validation — e.g. `page_size` outside 1–2000, or an unrecognised field.                         |
| `429`                   | Too Many Requests      | A rate limit was exceeded (see below).                                                                          |
| `500 / 502 / 503 / 504` | Server Error           | An unexpected server-side condition. Retry after a short delay.                                                 |

## Error body

For most 4xx responses the body carries a stable machine-readable `code` and a human-readable `message` under `detail`:

```json
{
  "detail": {
    "code": "INVALID_CREDENTIALS",
    "message": "Invalid app id or API key."
  }
}
```

Switch on `detail.code` (stable) rather than the message text (which may be retuned). Body-validation failures (`422`) use FastAPI's standard validation shape, with `detail` as an array describing each invalid field.

| Code                   | Status | When                                                                                                  |
| ---------------------- | ------ | ----------------------------------------------------------------------------------------------------- |
| `MISSING_CREDENTIALS`  | 401    | No `app_id` in the body, or no `X-Api-Key` header.                                                    |
| `INVALID_CREDENTIALS`  | 401    | The key doesn't match the app (unknown app and wrong key share this code so app ids can't be probed). |
| `INVALID_REQUEST`      | 400    | A bad cursor, unknown filter attribute, or unsupported aggregation.                                   |
| `INVALID_CONTENT_TYPE` | 415    | `Content-Type` wasn't `application/json`.                                                             |
| `RATE_LIMITED`         | 429    | A rate limit was exceeded.                                                                            |

## Rate limiting

**\[status code: 429]** — limits are enforced per API key (each app has exactly one key, so this is effectively per app):

- A maximum of **5 requests per second**
- A maximum of **500 requests per hour**
- A maximum of **2000 records per request**

When a limit is exceeded the API returns `429 RATE_LIMITED`. The per-second window clears within a second and the hourly window rolls forward each hour, so back off briefly and retry. A per-second burst that's rejected doesn't consume your hourly budget.

### Rate-limit headers

Authenticated responses advertise your current hourly budget so you can pace requests _before_ hitting a limit. They appear on a successful `200` and on a `429`; a request rejected _before_ authentication (e.g. `401` for missing/invalid credentials, or `415` for the wrong content type) carries no budget headers, because no key has been metered yet:

| Header                  | Meaning                                 |
| ----------------------- | --------------------------------------- |
| `X-RateLimit-Limit`     | Your hourly request budget.             |
| `X-RateLimit-Remaining` | Requests remaining in the current hour. |
| `X-RateLimit-Reset`     | Seconds until the hourly window resets. |

A `429` additionally returns a `Retry-After` header — the seconds to wait before retrying (`1` for a per-second burst, or the seconds until the hourly window resets).

```http
X-RateLimit-Limit: 500
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1800
Retry-After: 1800
```

<Callout icon="📘" theme="info">
  ### Note

  To stay under the limits, do more per call: combine several filters, groupings, and aggregations into a single analytics request instead of firing many small ones, and use `page_size` up to 2000 and request only the sections you need via `show_only`.
</Callout>

<br />
