---
title: Query Parameters
deprecated: false
hidden: true
metadata:
  robots: index
---
<Callout icon="📘" theme="info">
  ### Note

  All parameters travel in the JSON request body, and your API key rides in the `X-Api-Key` header. This page describes every field you can send in the body — operator syntax and the full filter grammar live on [Filter Operators](doc:filter-operators-1).
</Callout>

Send a JSON body with `Content-Type: application/json`. Unknown or misspelled fields are rejected (the body is validated strictly), so a typo surfaces as a `422` rather than being silently ignored.

## Common body fields (list endpoints)

These apply to `/session`, `/user`, and `/event`.

| Field        | Type    | Default                  | Description                                                                                                |
| ------------ | ------- | ------------------------ | ---------------------------------------------------------------------------------------------------------- |
| `app_id`     | string  | —                        | **Required.** The app to read. The API key rides in the `X-Api-Key` header.                                |
| `filters`    | array   | `[]`                     | Filter objects `{attribute, operator, value}`. Omit for the default last-30-days window.                   |
| `show_only`  | array   | per endpoint (see below) | Which response sections to include. Omit for the endpoint's lean **default set**; pass it to request more. |
| `page_size`  | integer | `500`                    | Records per page, `1`–`2000`.                                                                              |
| `cursor`     | string  | `null`                   | Opaque cursor for the next page. Omit for the first page.                                                  |
| `with_video` | boolean | `false`                  | _Sessions only._ Include a signed replay `video` link per session.                                         |

## Pagination

### Cursor pagination (lists)

List endpoints stream results newest-first with a cursor. Set `page_size` (1–2000) and, for pages after the first, pass the `next_cursor` from the previous response as `cursor`.

```json
// first page
{ "app_id": "YOUR_APP_ID", "page_size": 100 }

// next page — reuse next_cursor from the previous response
{ "app_id": "YOUR_APP_ID", "page_size": 100, "cursor": "eyJyZWNvcmRlZG9uIjoiMjAyNi…" }
```

<Callout icon="🚧" theme="warn">
  ### Important

  The cursor is opaque and tied to the query that produced it. Pass it back verbatim; don't decode, edit, or hand-build it. A malformed cursor returns `400 INVALID_REQUEST`.
</Callout>

### Offset pagination (analytics)

Analytics endpoints return a bounded, grouped result set and paginate by number: send `page` (1-indexed) and `page_size`. The response reports `{current, next, total}`.

## filters

Narrow results with an array of filter objects. Each object names an `attribute`, an `operator`, and a `value`:

```json
"filters": [
  { "attribute": "device_country", "operator": "equal", "value": "USA" }
]
```

Multiple filters are combined with AND:

```json
"filters": [
  { "attribute": "device_country", "operator": "equal", "value": "USA" },
  { "attribute": "session_duration", "operator": "greater", "value": 30 }
]
```

<Callout icon="📘" theme="info">
  ### Note

  Filtering by an attribute the endpoint doesn't recognise fails closed with `400` — you learn the filter was invalid instead of quietly getting unfiltered results. See each endpoint page for its supported attributes and [Filter Operators](doc:filter-operators-1) for operator syntax.
</Callout>

### Date range

Scope the query window with a date filter inside `filters`. When no date filter is present, the API defaults to the **last 30 days**.

```json
"filters": [
  { "attribute": "date_range", "operator": "between_dates",
    "value": { "lower": "2026-06-01", "upper": "2026-06-30" } }
]
```

<br />

### Date filter operators

| Operator                    | Meaning                                                                |
| --------------------------- | ---------------------------------------------------------------------- |
| `between` / `between_dates` | Both bounds, inclusive. Accepts `{"lower","upper"}` or `["from","to"]` |
| `since` / `after`           | Inclusive lower bound; upper stays open                                |
| `before` / `until`          | Inclusive upper bound; lower filled 30 days back                       |
| `on` / `noton`              | A single day                                                           |

`since` and `after` are aliases, as are `before` and `until` — all inclusive.

The window filter must use `attribute: "date_range"`. A date-shaped filter on any other attribute (for example `session_uploadedon`) is treated as a normal predicate, not as the query window.

A malformed date, or an inverted range where `lower` is after `upper`, returns `400`. The API does not silently fall back to the default window.

### Dates and timezones

**Dates are interpreted in your app's configured analytics timezone** (Settings → Analytics defaults) — not UTC, and not your browser's timezone. Apps with no configured timezone default to UTC.

For an app set to `Asia/Katmandu` (UTC+05:45), a range of `2024-11-01` → `2024-11-30` selects activity from `2024-10-31 18:15:00Z` through `2024-11-30 18:14:59Z`.

When only one bound is supplied, the other is filled from **today in your app's timezone**.

## show_only

Each record is grouped into named sections. **Omitting&#x20;**`show_only`**&#x20;returns the endpoint's default set** — the core data most integrations need, and the fastest response shape. Pass `show_only` with any combination of sections to request more (or different) blocks. Identity fields (ids, names, timestamps, `url`) are always present on every record regardless of `show_only`.

| Endpoint   | Available sections                                                                                                                                                                                      | Default when omitted                   |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- |
| `/session` | `property`, `user`, `location`, and device sub-sections `deviceBasics`, `deviceHardware`, `devicePerformance`, `deviceNetwork`, `deviceSecurity`, `deviceSettings` (or `device` to request all of them) | `["property"]`                         |
| `/user`    | `property`, `usage`, `location`, `deviceBasics`, `deviceHardware` (or `device` for both)                                                                                                                | `["usage"]`                            |
| `/event`   | `sessionProperty`, `userProperty`, `eventProperty`, `deviceBasics`, `deviceHardware` (or `device` for both)                                                                                             | `["eventProperty", "sessionProperty"]` |

<Callout icon="📘" theme="info">
  ### Note

  For full extractions (data-lake sync), list every section explicitly. Requesting fewer sections makes responses significantly smaller and faster — a sessions page with only `property` is roughly a third of the size of a full one.
</Callout>

<Callout icon="📘" theme="info">
  ### Device sub-sections

  The device data is split into purpose-built sub-sections so you can pull only
  what you need. `device` is a convenience alias that expands to every device
  sub-section the endpoint supports.

  | Sub-section         | Contents (mobile)                                                                            | Web                                           |
  | ------------------- | -------------------------------------------------------------------------------------------- | --------------------------------------------- |
  | `deviceBasics`      | `osName`, `osVersion`, `appVersion`, `sdkVersion`, `deviceId`, `type`, `language`, `country` | also `browser`, `browserVersion`              |
  | `deviceHardware`    | `manufacturer`, `dpi`, `width`, `height`, `model`, `class`, `platform`                       | `manufacturer`, `dpi`, `width`, `height` only |
  | `devicePerformance` | `totalRamInMB`, `freeRamInMB`, `totalStorageInMB`                                            | empty `{}`                                    |
  | `deviceNetwork`     | `carrierCode`, `carrierName`                                                                 | empty `{}`                                    |
  | `deviceSecurity`    | `isRooted`                                                                                   | empty `{}`                                    |
  | `deviceSettings`    | `isNotificationEnabled`                                                                      | empty `{}`                                    |

  `/session` supports all six sub-sections; `/user` and `/event` support
  `deviceBasics` and `deviceHardware`.

  <Callout icon="📘" theme="info">
    ### Tip

    Request only the sub-sections you need to keep responses small — e.g.
    `deviceBasics` for OS/app info, adding `devicePerformance` (RAM/storage) or
    `deviceNetwork` (carrier) only when required. On `/session`, web records return
    empty objects `{}` for the mobile-only sub-sections.
  </Callout>
</Callout>

```json
{ "app_id": "YOUR_APP_ID", "show_only": ["property", "device"] }
```

## Analytics-only fields

The `/analytics` endpoints accept three additional fields. Full syntax is on [Filter Operators](doc:filter-operators-1).

| Field         | Type    | Description                                                                                                                        |
| ------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `group_by`    | array   | Dimensions to break the numbers down by, e.g. `[{"attribute": "device_model"}]`. Optional `max_group_number` caps groups (max 50). |
| `aggregation` | array   | Metrics to compute, e.g. `[{"attribute": "session_duration", "operator": "avg"}]`. Omit for the endpoint's default metric set.     |
| `comparison`  | boolean | When `true`, adds period-over-period % change. Honored only when `group_by` is empty.                                              |

### Valid `group_by` attributes

Maximum **2** dimensions per request. An unrecognized attribute returns `400 INVALID_REQUEST`.

`/session/analytics`**&#x20;and&#x20;**`/user/analytics`

`device_model`, `device_manufacturer`, `device_class`, `device_os_version`, `device_os_name`, `device_type`, `app_version`, `sdk_version`, `device_platform`, `device_country`, `device_city`, `browser_name`, `browser_version`, `session_recorded_month`, `session_recorded_week`, `session_recordedon_day`

`/event/analytics`

`event_name`, `event_screen_name`, `device_model`, `device_manufacturer`, `device_class`, `device_os_name`, `device_os_version`, `device_type`, `app_version`, `device_platform`, `browser_name`, `browser_version`, `event_recorded_month`, `event_recorded_week`, `event_recordedon_day`

`/screen/analytics`

`screen_name`, `previous_screen`, `next_screen`, `app_version`, `device_model`, `device_manufacturer`, `device_class`, `device_os_name`, `device_os_version`, `device_type`, `sdk_version`, `device_country`, `device_platform`, `browser_name`, `browser_version`, `screen_recorded_month`, `screen_recorded_week`, `screen_recordedon_day`

The `*_recorded_month`, `*_recorded_week`, and `*_recordedon_day` buckets are cut on `recordedon` — when the activity happened on the device — in your app's configured analytics timezone, not on server upload time.

### References:

[Sessions](doc:session-endpoints)
[Users](doc:users-2)
[Events](doc:events-2)
[Screen Analytics](doc:screens)<br />[Filter Operators](doc:filter-operators-1)
[Error Handling & Messages](doc:error-handling-and-messages-1)
