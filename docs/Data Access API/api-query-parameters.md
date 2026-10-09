---
title: Query Parameters
excerpt: Filtering, pagination, and aggregation options for the Data Access API
deprecated: false
hidden: false
metadata:
  title: 'Query Parameters - UXCam Data Access API'
  description: 'Learn how to filter, paginate, and aggregate data in UXCam API requests'
  robots: index
next:
  description: ''
---

# Query Parameters

All Data Access API endpoints support common query parameters for filtering, pagination, and aggregation.

---

## Pagination

Control how many results are returned and which page to fetch.

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| `page` | Integer | Page number (1-indexed) | 1 |
| `page_size` | Integer | Results per page (max 500) | 50 |

### Example

```bash
curl "https://api.uxcam.com/v2/session" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'page=2' \
  --data-urlencode 'page_size=100'
```

---

## Filters

Narrow results to specific criteria. Filters are passed as a JSON array.

### Filter Structure

```json
{
  "attribute": "attribute_name",
  "operator": "operator_type",
  "value": "filter_value"
}
```

### Common Operators

| Operator | Description | Example Value |
|----------|-------------|---------------|
| `equal` | Exact match | `"USA"` |
| `not_equal` | Not equal | `"USA"` |
| `in` | Match any in list | `["USA", "UK", "CA"]` |
| `not_in` | Match none in list | `["test", "demo"]` |
| `greater` | Greater than | `100` |
| `less` | Less than | `100` |
| `between_dates` | Date range | `{"lower": "2024-01-01", "upper": "2024-01-31"}` |

See [Filter Operators](/docs/filter-operators) for the complete list.

### Example

```bash
# Sessions from USA with duration > 60 seconds
curl "https://api.uxcam.com/v2/session" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'filters=[
    {"attribute":"device_country","operator":"equal","value":"USA"},
    {"attribute":"session_duration","operator":"greater","value":60}
  ]'
```

---

## Group By (Analytics Only)

Aggregate results by specific attributes. Used with `/analytics` endpoints.

### Structure

```json
{
  "attribute": "attribute_name",
  "max_group_number": 50
}
```

### Example

```bash
# Session analytics grouped by device model
curl "https://api.uxcam.com/v2/session/analytics" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'group_by=[{"attribute":"device_model","max_group_number":20}]'
```

### Common Group Attributes

| Attribute | Description |
|-----------|-------------|
| `device_model` | Group by device model |
| `device_platform` | Group by iOS/Android |
| `device_country` | Group by country |
| `app_version` | Group by app version |
| `session_uploadedon_day` | Group by day |
| `session_uploaded_week` | Group by week |
| `session_uploaded_month` | Group by month |

---

## Aggregation (Analytics Only)

Specify which metrics to calculate.

### Example

```bash
curl "https://api.uxcam.com/v2/session/analytics" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'aggregation=[{"attribute":"session_count"}]'
```

### Common Aggregations

| Attribute | Description |
|-----------|-------------|
| `session_count` | Total sessions |
| `session_unique_user_count` | Unique users |
| `avg_session_duration` | Average session length |
| `avg_session_gesture_count` | Average interactions |

---

## Comparison (Analytics Only)

Compare current period to previous period.

| Parameter | Type | Description |
|-----------|------|-------------|
| `comparison` | Integer | 1 = compare to previous period |

### Example

```bash
# Compare current month to previous month
curl "https://api.uxcam.com/v2/session/analytics" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'comparison=1'
```

---

## Date Range

Filter by date using the `date_range` attribute.

### Example

```bash
curl "https://api.uxcam.com/v2/session" \
  -G \
  --data-urlencode 'appid=YOUR_APP_ID' \
  --data-urlencode 'apikey=YOUR_API_KEY' \
  --data-urlencode 'filters=[
    {
      "attribute": "date_range",
      "operator": "between_dates",
      "value": {
        "lower": "2024-01-01T00:00:00Z",
        "upper": "2024-01-31T23:59:59Z"
      }
    }
  ]'
```

---

## See Also

- [Filter Operators](/docs/filter-operators) - Complete operator reference
- [Sessions Endpoint](/docs/sessions) - Session-specific parameters
- [Users Endpoint](/docs/users) - User-specific parameters
- [Events Endpoint](/docs/events-endpoint) - Event-specific parameters
