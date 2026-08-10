---
title: Screen Analytics
deprecated: false
hidden: true
metadata:
  robots: index
---
A **screen** is a single view in your app (e.g. `ProductDetailRoute`). The Screens endpoint returns aggregated analytics for each screen — engagement (visits, users, time on screen), health (crash rate, UI-freeze rate), and navigation (entries, exits, bounce) — the same view you see on the Screens dashboard.

<Callout icon="📘" theme="info">
  ### Analytics only

  A screen is an aggregate — one row sums many individual visits, exactly like the Screens dashboard and the CSV export. So Screens has a single, aggregated endpoint that returns **one row per screen**. There is no raw per-visit list endpoint.
</Callout>

## Endpoint

`POST /api/data-access/v1/screen/analytics`

Returns per-screen numbers, optionally grouped by additional dimension(s). By default the response is grouped by `screen_name` (one row per screen), ordered by most-visited, for the last 30 days.

## Default metrics

Returned when `aggregation` is omitted:

| Metric               | Description                                                                     |
| -------------------- | ------------------------------------------------------------------------------- |
| `screen_visits`      | Total visits to the screen                                                      |
| `unique_users`       | Distinct users who opened the screen                                            |
| `unique_sessions`    | Distinct sessions the screen appeared in                                        |
| `avg_time_on_screen` | Average seconds spent on the screen per visit                                   |
| `sum_time_on_screen` | Total seconds spent on the screen across all visits                             |
| `rage_taps`          | Total rage taps recorded on the screen                                          |
| `crash_rate`         | Percentage of visits where the app crashed on this screen                       |
| `ui_freeze_rate`     | Percentage of visits with at least one UI freeze                                |
| `entries`            | Visits that were the session's first screen (the app opened here)               |
| `exits`              | Visits where the user left the app from this screen                             |
| `exit_rate`          | Percentage of visits that ended in an app exit                                  |
| `bounce_rate`        | Percentage of very short visits (under 1s) that returned to the previous screen |

## Additional metrics

Pass an `aggregation` array to choose exactly which metrics you want. Beyond the default set:

| Attribute               | Operator(s)   | Description                                                                             |
| ----------------------- | ------------- | --------------------------------------------------------------------------------------- |
| `time_on_screen`        | `avg` / `sum` | Time on screen — `avg` returns `avg_time_on_screen`, `sum` returns `sum_time_on_screen` |
| `median_time_on_screen` | `count`       | Median (P50) seconds on screen                                                          |
| `p95_time_on_screen`    | `count`       | 95th-percentile seconds on screen                                                       |
| `avg_taps`              | `count`       | Average taps per visit                                                                  |
| `avg_swipes`            | `count`       | Average swipes per visit                                                                |
| `avg_time_before_exit`  | `count`       | Average seconds on screen before an app exit                                            |
| `quick_exit_rate`       | `count`       | Percentage of visits under 3s that ended in an app exit                                 |

<Callout icon="📘" theme="info">
  ### Picking metrics

  Every default and count-style metric is requested by name — `{"attribute":"crash_rate"}`. Only `time_on_screen` takes an operator — `{"attribute":"time_on_screen","operator":"avg"}`. Omit `aggregation` (or pass `[]`) for the full default set above.
</Callout>

## Group-by dimensions

Pass in `group_by` (up to two). Omit it for the default `screen_name` grouping (one row per screen):

- **Screen** — `screen_name`, `previous_screen`, `next_screen`
- **Device** — `device_model`, `device_manufacturer`, `device_class`, `device_os_name`, `device_os_version`, `device_type`, `device_platform`
- **App / SDK** — `app_version`, `sdk_version`
- **Location** — `device_country`
- **Browser** (web) — `browser_name`, `browser_version`
- **Time bucket** — `screen_uploaded_month`, `screen_uploaded_week`, `screen_uploadedon_day`

`device_platform` groups return readable values (`Android` / `iOS` / `Web`).

<Callout icon="📘" theme="info">
  ### How many screens come back

  A query returns up to **50 groups**, ordered by the first metric (by default, the 50 most-visited screens). Use `max_group_number` on a `group_by` entry to ask for fewer.
</Callout>

## Filtering

Scope results with a `filters` array. A `date_range` filter sets the window (omit for the last 30 days). Common attributes — `device_country`, `device_platform`, `device_model`, `app_version` — narrow the result; see [Filter Operators](/docs/filter-operators-1) for the full operator syntax.

## Analyze screens

`POST /api/data-access/v1/screen/analytics`

### Request

Send a JSON body; the API key rides in the `X-Api-Key` header.

- `app_id` — required; the app to read.
- `filters` — optional; a `date_range` window plus the attributes above (omit for the last 30 days).
- `group_by` — up to two dimensions; omit for the default `screen_name`.
- `aggregation` — the metrics to return; omit for the default set.
- `page` / `page_size` — offset pagination over the grouped rows (`page` default `1`; `page_size` `1`–`2000`, default `500`).

```bash
curl -X POST https://tara.uxcam.com/api/data-access/v1/screen/analytics \
  -H "X-Api-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"app_id":"YOUR_APP_ID","group_by":[{"attribute":"screen_name","max_group_number":50}]}'
```

### Response

```json
{
  "success": true,
  "data": [
    {
      "screen_name": "MainRoute",
      "screen_visits": 12100000,
      "unique_users": 842301,
      "unique_sessions": 1980554,
      "avg_time_on_screen": 8.31,
      "sum_time_on_screen": 100530000,
      "rage_taps": 41022,
      "crash_rate": 0.42,
      "ui_freeze_rate": 1.10,
      "entries": 1450332,
      "exits": 210665,
      "exit_rate": 10.64,
      "bounce_rate": 3.21,
      "dashboard_link": "https://tara.uxcam.com/app/YOUR_APP_ID/screens"
    },
    {
      "screen_name": "ProductDetailRoute",
      "screen_visits": 9600000,
      "unique_users": 701244,
      "unique_sessions": 1622890,
      "avg_time_on_screen": 14.07,
      "sum_time_on_screen": 135072000,
      "rage_taps": 88210,
      "crash_rate": 1.18,
      "ui_freeze_rate": 2.03,
      "entries": 120044,
      "exits": 356701,
      "exit_rate": 21.98,
      "bounce_rate": 5.44,
      "dashboard_link": "https://tara.uxcam.com/app/YOUR_APP_ID/screens"
    }
  ],
  "pagination": { "current": 1, "next": null, "total": 42 }
}
```

<Callout icon="📘" theme="info">
  ### Note

  A request with just `app_id` (no `group_by`, no `aggregation`) returns one row per screen with all default metrics. Requesting a subset of metrics returns only those keys.
</Callout>

## Errors

Errors use the shared `{ "detail": { "code", "message" } }` shape — a missing or invalid key is `401`, a malformed body is `422`, and an unsupported metric or dimension is `400`. See [Error Handling & Messages](/docs/error-handling-and-messages-1) for the full list.

### References:

[Sessions](doc:session-endpoints)
[Users](doc:users-2)
[Events](doc:events-2)<br />[Query Parameters](doc:query-parameters-1)
[Filter Operators](doc:filter-operators-1)
[Error Handling & Messages](doc:error-handling-and-messages-1)