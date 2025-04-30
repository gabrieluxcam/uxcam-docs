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

<Table>
  <thead>
    <tr>
      <th>
        Status Code
      </th>

      <th>
        Reason
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        200
      </td>

      <td>
        OK
      </td>

      <td>
        The HTTP 200 OK success status response code indicates that the request has succeeded
      </td>
    </tr>

    <tr>
      <td>
        400
      </td>

      <td>
        Bad requests
      </td>

      <td>
        This response status code indicates that the server cannot or will not process the request due to a client error (for example, malformed request syntax, invalid request message framing, or deceptive request routing)
      </td>
    </tr>

    <tr>
      <td>
        401
      </td>

      <td>
        Unauthorised requests
      </td>

      <td>
        This response status code indicates that the client request lacks valid authentication credentials for the resource.  

        Missing valid API Key or App ID key
      </td>
    </tr>

    <tr>
      <td>
        403
      </td>

      <td>
        Forbidden requests
      </td>

      <td>
        This response status code indicates that the server understands the request but refuses to authorise it
      </td>
    </tr>

    <tr>
      <td>
        404
      </td>

      <td>
        Resource Not Found
      </td>

      <td>
        The requested resource doesn't exist in the server
      </td>
    </tr>

    <tr>
      <td>
        429
      </td>

      <td>
        Request limit exceeded
      </td>

      <td>
        This response status code indicates the user has sent too many requests in a given amount of time
      </td>
    </tr>

    <tr>
      <td>
        500, 502, 503, 504
      </td>

      <td>
        Internal Server Error
      </td>

      <td>
        These are server error response codes that indicate the server encountered an unexpected condition that prevented it from fulfilling the requests
      </td>
    </tr>
  </tbody>
</Table>

## Rate Limiting

**\[status code: 429]\:**

This means you've made frequent calls to an API that exceeded the call rate limit. UXCam has implemented the following **rate limit**.

* A maximum of five concurrent requests per second
* A maximum of 500 requests per hour
* A Total number of 500 records can be fetched on a single request.

If any of the above-mentioned conditions are met regarding the rate limit, then APIs will start to throw a 429 status code. **This limit will only be reset every 24 hours.**

> 📘 Note
>
> To minimise rate-limiting errors, consider combining multiple filters, groupings, and aggregations into a single request to make multiple queries.
