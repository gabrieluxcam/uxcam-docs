---
title: Filter Operators
excerpt: Operators for filtering, grouping, and aggregating — by data type.
deprecated: false
hidden: true
metadata:
  robots: index
---
Filters are objects of the form `{ "attribute", "operator", "value" }` passed in the `filters` array. The operator you may use depends on the attribute's data type. Analytics endpoints additionally accept `group_by` and `aggregation`.

## Integer / Float operators

For numeric attributes such as `session_duration`.

| Operator           | Meaning | Value type      |
| ------------------ | ------- | --------------- |
| `greater`          | >       | Integer / Float |
| `less`             | \<      | Integer / Float |
| `greater_or_equal` | >=      | Integer / Float |
| `less_or_equal`    | \<=     | Integer / Float |

## String operators

| Operator       | Meaning                    | Value type       |
| -------------- | -------------------------- | ---------------- |
| `equal`        | =                          | String           |
| `not_equal`    | ≠                          | String           |
| `in`           | ∈ (in set)                 | Array of strings |
| `not_in`       | ∉ (not in set)             | Array of strings |
| `contains`     | contains substring         | String           |
| `not_contains` | does not contain substring | String           |

## List operators

For list-of-string attributes such as `session_screen_list`.

| Operator      | Meaning            | Value type       |
| ------------- | ------------------ | ---------------- |
| `has_any`     | intersects         | Array of strings |
| `not_has_any` | does not intersect | Array of strings |

## DateTime operators

Date filters use the `date_range` attribute and scope the whole query window. **If no date filter is supplied, the response covers the last 30 days.** Dates are ISO-8601; a date (`2026-06-01`) or a zulu timestamp (`2026-06-01T11:23:12Z`) are both accepted.

| Operator           | Window it selects     | Value shape                              |
| ------------------ | --------------------- | ---------------------------------------- |
| `between_dates`    | lower … upper         | `{"lower": "<date>", "upper": "<date>"}` |
| `in`               | first … second        | `["<lower>", "<upper>"]`                 |
| `on` / `not_on`    | a single day          | `"<date>"`                               |
| `after` / `since`  | value … today         | `"<date>"`                               |
| `before` / `until` | (value − 30d) … value | `"<date>"`                               |

```json
{ "attribute": "date_range", "operator": "between_dates",
  "value": { "lower": "2026-06-01", "upper": "2026-06-30" } }
```

📘 Which timestamp the window uses
List endpoints (/session, /user, /event) scope the window by upload time — when the device delivered the data. Analytics endpoints (/…/analytics) scope by record time — when the activity happened on the device. Because a session can be recorded and uploaded on different days (offline usage, delayed sync), the same date_range bounds can return slightly different counts on a list vs. its analytics counterpart. This is expected.

## Custom-property filters (JSON attributes)

Custom user and event properties are keyed maps. Filter them with the `user_custom_property` / `event_custom_property` attribute plus a `property_name`:

```json
{ "attribute": "user_custom_property", "operator": "equal",
  "property_name": "loyalty_card", "value": "gold" }

{ "attribute": "event_custom_property", "operator": "greater",
  "property_name": "price_cents", "value": 1000 }
```

<Callout icon="📘" theme="info">
  ### Note

  Custom-property _values_ are stored and returned as strings. Compare against string values (e.g. `"1499"`) for exact matches.
</Callout>

<Callout icon="📘" theme="info">
  ### Filtering only

  Custom properties can be **filtered** (as above) but are not `group_by` or `aggregation` dimensions — a grouping or aggregation entry takes only a documented `attribute` (no `property_name`). Each endpoint page lists its valid group-by and aggregation attributes.
</Callout>

## Groupings (analytics only)

`group_by` breaks the aggregated numbers down by one or more dimensions. Each entry has an `attribute` and an optional `max_group_number` that caps how many groups come back (default and maximum: **50**). Group by up to two dimensions per request.

```json
// single dimension
"group_by": [{ "attribute": "device_model" }]

// cap the number of groups
"group_by": [{ "attribute": "device_model", "max_group_number": 20 }]

// two dimensions
"group_by": [
  { "attribute": "device_platform" },
  { "attribute": "device_country", "max_group_number": 10 }
]
```

Each endpoint page lists its valid group-by attributes.

## Aggregation (analytics only)

`aggregation` selects which metrics to compute. Each entry pairs an `attribute` with an `operator`; an empty operator (or `"count"`) means a count. Omit `aggregation` entirely to get the endpoint's default metric set.

| Operator          | Function           |
| ----------------- | ------------------ |
| `count` (or `""`) | Row / entity count |
| `sum`             | Summation          |
| `avg`             | Average            |

```json
"aggregation": [
  { "attribute": "session_count", "operator": "count" },
  { "attribute": "session_duration", "operator": "avg" }
]
```

<Callout icon="📘" theme="info">
  ### Note

  Not every attribute supports every operator. Requesting an unsupported `(attribute, operator)` pair returns `400`. Each endpoint page lists the exact supported aggregations.
</Callout>

## Comparison (analytics only)

Set `comparison: true` to measure each metric against the **immediately preceding period of the same length** — a one-request way to answer "up or down versus last time?". For every metric in the result, the response adds a companion `<metric>_pct_change` field.

**How the change is computed:** `((current − previous) / previous) × 100`, rounded to two decimals. The value is `null` when there is no previous value or the previous value is `0` — so you never divide by zero.

<Callout icon="🚧" theme="warn">
  ### Ungrouped only

  `comparison` is honored **only when&#x20;**`group_by`**&#x20;is empty** — it applies to the single ungrouped total. If you send `comparison: true` together with `group_by`, the comparison is skipped and the grouped breakdown is returned instead.
</Callout>

### Request

```json
{ "app_id": "YOUR_APP_ID", "comparison": true }
```

### Response

Each metric is paired with its `_pct_change` (e.g. `session_count` → `session_count_pct_change`). A positive value means the metric rose versus the previous period, negative means it fell, and `null` means there was no comparable previous value.

```json
{
  "success": true,
  "data": [
    {
      "session_count": 12840,
      "session_count_pct_change": 8.42,
      "session_unique_user_count": 5310,
      "session_unique_user_count_pct_change": 3.17,
      "avg_session_duration": 74.22,
      "avg_session_duration_pct_change": -1.9
    }
  ],
  "pagination": { "current": 1, "next": null, "total": 1 }
}
```

### References:

[Sessions](doc:session-endpoints)
[Users](doc:users-2)
[Events](doc:events-2)
[Screen Analytics](doc:screens)
[Query Parameters](doc:query-parameters-1)<br />[Error Handling & Messages](doc:error-handling-and-messages-1)
