---
title: Query Parameters
deprecated: false
hidden: true
metadata:
  robots: index
---
<Callout icon="📘" theme="info">
  ### Note

  All parameters travel in the JSON request body, and your API key rides in the `X-Api-Key` header. This page describes every field you can send in the body — operator syntax and the full filter grammar live on [Filter Operators](doc:filter-operators).
</Callout>

Send a JSON body with `Content-Type: application/json`. Unknown or misspelled fields are rejected (the body is validated strictly), so a typo surfaces as a `422` rather than being silently ignored.

## Common body fields (list endpoints)

These apply to `/session`, `/user`, and `/event`.

| Field        | Type    | Default                  | Description                                                                                                |
| ------------ | ------- | ------------------------ | ---------------------------------------------------------------------------------------------------------- |
| `app_id`     | string  | —                        | **Required.** The app to read. The API key rides in the `X-Api-Key` header.                                |
| `filters`    | array   | `[]`                     | Filter objects `{attribute, operator, value}`. Omit for the default last-30-days window.                   |
| `show_only`  | array   | per endpoint (see below) | Which response sections to include. Omit for the endpoint's lean **default set**; pass it to request more. |
| `page_size`  | integer | `50`                     | Records per page, `1`–`2000`.                                                                              |
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

  Filtering by an attribute the endpoint doesn't recognise fails closed with `400` — you learn the filter was invalid instead of quietly getting unfiltered results. See each endpoint page for its supported attributes and [Filter Operators](doc:filter-operators) for operator syntax.
</Callout>

### Date range

Scope the query window with a date filter inside `filters`. When no date filter is present, the API defaults to the **last 30 days**.

```json
"filters": [
  { "attribute": "date_range", "operator": "between_dates",
    "value": { "lower": "2026-06-01", "upper": "2026-06-30" } }
]
```

## show\_only

Each record is grouped into named sections. **Omitting&#x20;**`show_only`**&#x20;returns the endpoint's default set** — the core data most integrations need, and the fastest response shape. Pass `show_only` with any combination of sections to request more (or different) blocks. Identity fields (ids, names, timestamps, `url`) are always present on every record regardless of `show_only`.

| Endpoint   | Available sections                                           | Default when omitted                   |
| ---------- | ------------------------------------------------------------ | -------------------------------------- |
| `/session` | `property`, `user`, `device`, `location`                     | `["property"]`                         |
| `/user`    | `property`, `usage`, `location`, `device`                    | `["usage"]`                            |
| `/event`   | `sessionProperty`, `userProperty`, `device`, `eventProperty` | `["eventProperty", "sessionProperty"]` |

<Callout icon="📘" theme="info">
  ### Note

  For full extractions (data-lake sync), list every section explicitly. Requesting fewer sections makes responses significantly smaller and faster — a sessions page with only `property` is roughly a third of the size of a full one.
</Callout>

```json
{ "app_id": "YOUR_APP_ID", "show_only": ["property", "device"] }
```

## Analytics-only fields

The `/analytics` endpoints accept three additional fields. Full syntax is on [Filter Operators](doc:filter-operators).

| Field         | Type    | Description                                                                                                                        |
| ------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `group_by`    | array   | Dimensions to break the numbers down by, e.g. `[{"attribute": "device_model"}]`. Optional `max_group_number` caps groups (max 50). |
| `aggregation` | array   | Metrics to compute, e.g. `[{"attribute": "session_duration", "operator": "avg"}]`. Omit for the endpoint's default metric set.     |
| `comparison`  | boolean | When `true`, adds period-over-period % change. Honored only when `group_by` is empty.                                              |

<br />
