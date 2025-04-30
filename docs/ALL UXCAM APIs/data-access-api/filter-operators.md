---
title: Filter Operators
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
## Integer Filter/Float Operators

These operators can only be used with filter attributes that are of type **integer** or **float**.

| Operator Name      | Operator Meaning | Operator Data Value Type |
| ------------------ | ---------------- | ------------------------ |
| greater            | >                | Integer/Float            |
| less               | \<               | Integer/Float            |
| greater\_or\_equal | >=               | Integer/Float            |
| less\_or\_equal    | \<=              | Integer/Float            |

## String Filter Operators

These operators can only be used with filter attributes that are of type **string**.

| Operator Name | Operator Meaning           | Operator Data Value Type |
| ------------- | -------------------------- | ------------------------ |
| equal         | =                          | String                   |
| not\_equal    | !=                         | String                   |
| in            | ∈ (element of set)         | List/Array of string     |
| not\_in       | ∉ (not an element of set)  | List/Array of string     |
| contains      | Contain substring          | String                   |
| not\_contains | Does not Contain substring | String                   |

## List Filter Operators

These operators can only be used with filter attributes that are of type **list of strings**. The only attribute utilised in list operators is **session\_screen\_list**.

| Operator Name | Operator Symbol | Operator Data Value Type |
| ------------- | --------------- | ------------------------ |
| has\_any      | ∈               | List/Array of string     |
| not\_has\_any | ∉               | List/Array of string     |

## DateTime Filter Operators

These operators can only be used with filter attributes that are of type **DateTime**. By default, the response will display data from the last 30 days.

**Note: Uxcam Data Access APIs support ISO-8601 format (zulu format)**

**Example: 2023-01-01,  2023-01-01T11:23:12Z**

| Operator Name  | Operator Symbol | Operator Data Value Type                                   |
| -------------- | --------------- | ---------------------------------------------------------- |
| on             | =               | DateTime String                                            |
| not\_on        | !=              | DateTime String                                            |
| after          | >               | DateTime String                                            |
| before         | \<              | DateTime String                                            |
| between\_dates | between\_dates  | Dictionary \{“lower”: “lower date”, “upper”:”upper\_date”} |

Filters are valid for all [User Data](https://developer.uxcam.com/docs/users-1) categories, [Session Data](https://developer.uxcam.com/docs/sessions-1) categories, and [Event Data](https://developer.uxcam.com/docs/events) categories having attribute data type (**string, integer, bool, Json, and  DateTime**)

In case of Attribute data type JSON filters need to be passed in the given format below. There are two data attributes having JSON type [user\_custom\_property](https://developer.uxcam.com/docs/send-user-properties) and [event\_custom\_property](https://developer.uxcam.com/docs/send-events)

```coffeescript Example
filters=[{"attribute":"user_custom_property","operator":
"<Operator Name>","property_name":"<custom property Name>","value":"<property value>"}]
filters=[{"attribute":"event_custom_property","operator":
"<Operator Name>","property_name":"<custom property Name>","value":"<property value>"}]
```

[Example](https://api.uxcam.com/v2/session?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&filters=\[%7B%22attribute%22:%20%22device_class%22,%22operator%22:%20%22equal%22,%20%22value%22:%22Android%20Large%22%7D]\&page=1\&page_size=20)

### Groupings (For analytics endpoints only)

Grouping is a method of organizing data attributes into groups and then displaying the distribution of those attributes or properties within the groups for a specific aggregation. Each individual group contains two keys: **attribute** & **max\_group\_number** (optional) (**Default value=50**) in the list of dictionaries.

Groupings are valid for all User Data categories, Session Data categories, and Event Data categories having attribute data type (**string, bool, and  DateTime String**)

```coffeescript Example
Single grouping:
group_by=[{"attribute":"<Attribute Name>"}]
group_by=[{"attribute":"<Attribute Name>","max_group_number":500}]
Two groupings:group_by=[{"attribute":"<Attribute Name 1>","max_group_number":500},
{"attribute":"<Attribute Name 2>","max_group_number":500}]

```

**Note:  At most two groupings can be allowed at once on request.**

In case of Attribute data type JSON, this need to be passed in the given format below. There are two data attributes having JSON type [user\_custom\_property](https://developer.uxcam.com/docs/send-user-properties) and [event\_custom\_property](https://developer.uxcam.com/docs/send-events)

```coffeescript Example
group_by=[{"attribute":"user_custom_property","property_name":"<custom property Name>"}]
group_by=[{"attribute":"event_custom_property","property_name":"<custom property Name>"}]
 

```

[Example](https://api.uxcam.com/v2/session/analytics?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&group_by=\[%7B%22attribute%22:%22app_version%22,%22max_group_number%22:50%7D]\&page=1\&page_size=50)

### Aggregation (For analytics endpoints only)

Aggregation is a process of combining one or more data attributes with aggregation operators. The output of an aggregation function is aggregated numerical values that can be used to create a statistical chart. Each individual aggregation contains two keys: **attribute** and **operator** in the list of dictionaries.

Aggregations are valid for all User Data categories, Session Data categories, and Event Data categories having attribute data type (**Count, Integer, and bool only**)

```
aggregation=[{"attribute":"Attribute Name","operator":"Operator Name"}]
```

Here are the valid aggregation functions:

| Aggregation Functions | Aggregation Operator Name | Valid Attribute Data Type |
| --------------------- | ------------------------- | ------------------------- |
| Summation             | sum                       | Integer, Bool             |
| Average               | avg                       | Integer, Bool             |
| Minimum               | min                       | Integer, Bool             |
| Maximum               | max                       | Integer, Bool             |

**Note:**  For JSON ([user\_custom\_property](https://developer.uxcam.com/docs/send-user-properties) and [event\_custom\_property](https://developer.uxcam.com/docs/send-events)) attribute data type, this supports aggregation functions like(sum/avg/min/max) as well if property\_name values contain integer values. If aggregation functions are used with string value then this will return an empty response \[[Example](https://api.uxcam.com/v2/user/analytics?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&aggregation=\[%7B%22attribute%22:%22user_custom_property%22,%22operator%22:%22avg%22,%22property_name%22:%22age_group%22%7D]\&page=1\&page_size=20)]

```coffeescript Sample for JSON type aggregation:
aggregation=[{"attribute":"user_custom_property","property_name":"price","operator":"avg"}]

aggregation=[{"attribute":"event_custom_property","property_name":"duration","operator":"avg"}]

```

[Example](Link)

### Comparison (Optional)(For analytics endpoints only)

In the case of the comparison parameter, if it’s set to **true**, the response will include a percentage change compared to the previous date. The previous date is a specific time period before the selected range. 

For example, if the current date range is set to one month, the previous date will be the preceding month.

```
comparison=1

```

**Note**: This parameter is only applicable to the analytics endpoint and cannot be used in conjunction with grouping parameters. If both grouping and comparison are used, the comparison parameter will be skipped.

[Example](https://api.uxcam.com/v2/session/analytics?appid=60f6c0b8b97ba419120b82eb\&apikey=9c633412-927a-4f4e-87bc-386dc1e3a618\&comparison=1)
