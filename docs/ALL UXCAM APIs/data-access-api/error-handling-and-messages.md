---
title: Error Handling and Messages
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---
UXCam server issues a **status code** for every request made by its clients. The status of the response can be decoded using the table below.



[block:parameters]
{
  "data": {
    "h-0": "Status Code",
    "h-1": "Reason",
    "h-2": "Description",
    "0-0": "200",
    "0-1": "OK",
    "0-2": "The HTTP 200 OK success status response code indicates that the request has succeeded",
    "1-0": "400",
    "1-1": "Bad requests",
    "1-2": "This response status code indicates that the server cannot or will not process the request due to a client error (for example, malformed request syntax, invalid request message framing, or deceptive request routing)",
    "2-0": "401",
    "2-1": "Unauthorised requests",
    "2-2": "This response status code indicates that the client request lacks valid authentication credentials for the resource.  \n  \nMissing valid API Key or App ID key",
    "3-0": "403",
    "3-1": "Forbidden requests",
    "3-2": "This response status code indicates that the server understands the request but refuses to authorise it",
    "4-0": "404",
    "4-1": "Resource Not Found",
    "4-2": "The requested resource doesn't exist in the server",
    "5-0": "429",
    "5-1": "Request limit exceeded",
    "5-2": "This response status code indicates the user has sent too many requests in a given amount of time",
    "6-0": "500, 502, 503, 504",
    "6-1": "Internal Server Error",
    "6-2": "These are server error response codes that indicate the server encountered an unexpected condition that prevented it from fulfilling the requests"
  },
  "cols": 3,
  "rows": 7,
  "align": [
    null,
    null,
    null
  ]
}
[/block]

## Rate Limiting



**[status code: 429]\:**

This means you've made frequent calls to an API that exceeded the call rate limit. UXCam has implemented the following **rate limit**.

- A maximum of five concurrent requests per second
- A maximum of 500 requests per hour
- A Total number of 500 records can be fetched on a single request.

If any of the above-mentioned conditions are met regarding the rate limit, then APIs will start to throw a 429 status code. **This limit will only be reset every 24 hours.**

> 📘 Note
> 
> To minimise rate-limiting errors, consider combining multiple filters, groupings, and aggregations into a single request to make multiple queries.