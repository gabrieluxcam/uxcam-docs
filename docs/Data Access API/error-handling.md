---
title: Error Handling
excerpt: HTTP status codes and error messages
deprecated: false
hidden: false
metadata:
  title: 'Error Handling - UXCam Data Access API'
  description: 'Understanding error responses from the UXCam Data Access API'
  robots: index
next:
  description: ''
---

# Error Handling

The Data Access API uses standard HTTP status codes to indicate success or failure.

---

## HTTP Status Codes

| Code | Status | Description |
|------|--------|-------------|
| 200 | OK | Request succeeded |
| 400 | Bad Request | Invalid parameters or malformed request |
| 401 | Unauthorized | Invalid or missing API credentials |
| 403 | Forbidden | Valid credentials but insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server-side error |

---

## Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "INVALID_FILTER",
    "message": "Invalid filter attribute: unknown_field"
  }
}
```

---

## Common Errors

### Authentication Errors

**401 Unauthorized**

```json
{
  "success": false,
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Invalid API key"
  }
}
```

**Solution**: Verify your App ID and API Key are correct. Check they haven't been regenerated in the dashboard.

---

### Validation Errors

**400 Bad Request - Invalid Filter**

```json
{
  "success": false,
  "error": {
    "code": "INVALID_FILTER",
    "message": "Unknown attribute: invalid_attribute"
  }
}
```

**Solution**: Check the attribute name against the documented attributes for the endpoint.

---

**400 Bad Request - Invalid Operator**

```json
{
  "success": false,
  "error": {
    "code": "INVALID_OPERATOR",
    "message": "Operator 'contains' not supported for attribute 'session_duration'"
  }
}
```

**Solution**: Use a compatible operator for the attribute type. See [Filter Operators](filter-operators).

---

### Rate Limit Errors

**429 Too Many Requests**

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Please wait before making more requests."
  }
}
```

**Solution**: Wait before retrying. Implement exponential backoff. Consider caching responses.

---

## Best Practices

### Implement Retry Logic

```python
import time
import requests

def api_request(url, headers, params, max_retries=3):
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers, params=params)

        if response.status_code == 200:
            return response.json()

        if response.status_code == 429:
            wait_time = 2 ** attempt  # Exponential backoff
            time.sleep(wait_time)
            continue

        # Other errors - don't retry
        response.raise_for_status()

    raise Exception("Max retries exceeded")
```

### Validate Before Sending

```python
VALID_OPERATORS = ['equal', 'not_equal', 'in', 'not_in', 'greater_than', 'less_than']

def validate_filter(filter_obj):
    if filter_obj['operator'] not in VALID_OPERATORS:
        raise ValueError(f"Invalid operator: {filter_obj['operator']}")
    return True
```

### Log Errors for Debugging

```python
import logging

logger = logging.getLogger(__name__)

try:
    response = api_request(url, headers, params)
except requests.HTTPError as e:
    logger.error(f"API error: {e.response.status_code} - {e.response.text}")
    raise
```

---

## See Also

- [Query Parameters](api-query-parameters) - Valid filter syntax
- [Filter Operators](filter-operators) - Supported operators by type
