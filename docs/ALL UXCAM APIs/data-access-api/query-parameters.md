---
title: Query Parameters
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
UXCam Data Access APIs accept the following request parameters on each and every endpoint.

> 🚧 Please do not use whitespace in request query parameters.

## Pagination:

This pagination parameter divides a large data set into smaller sections to facilitate the handling of JSON responses. This parameter supports **integer** values only.

**page(Default Value=1):** a page refers to a portion of the data set that is returned in response to the API request. Typically, The API request will retrieve one page of data, and additional pages can be accessed by specifying a different "page number" in subsequent API requests.

**page\_size(Default Value=50 and max value = 500):**  Page size defines the size of pages that are used to limit the total number of records in the APIs response.

**Note: If the pagination parameters are not passed then the APIs response returned the first page with a total number of 50 records on the page.**

```c Example
page=1&page_size=20
```

Example Request [Here](https://api.uxcam.com/v2/session?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&page=1\&page_size=20).

## Filters:

Filter parameter is used to narrow down the results by a combination of filter attribute name, filter operator, and filter value. They are applied as a list of dictionaries, where each dictionary represents a separate filter. Each individual filter contains three keys: **attribute**, **operator**, and **value** in the dictionary.

```c Single filter sample:
filters=[{"attribute":"Attribute Name","operator":"Operator Name","value":"Filter value"}]
```

```c Multiple filter sample:
filters=[{"attribute":"Attribute Name","operator":"Operator Name","value":"Filter value"},{"attribute":"Attribute Name","operator":"Operator Name","value":"Filter value"}]
```
