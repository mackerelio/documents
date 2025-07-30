---
Title: Traces
Date: 2025-07-24T12:50:00+09:00
URL: https://mackerel.io/api-docs/entry/traces
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/6802418398528132533
---

<ul class="internal-nav">
  <li><a href="#get">Get Trace</a></li>
</ul>

<h2 id="get">Get Trace</h2>

Get detailed trace information for the specified trace ID.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/traces/<em>&lt;traceId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "spans": [
    {
      "traceId": "0123456789abcdef0123456789abcdef",
      "spanId": "012345678012345678",
      "traceState": "congo=xx,key=val",
      "name": "test-span",
      "kind": "internal",
      "startTime": "2025-07-09T14:03:02.000Z",
      "endTime": "2025-07-09T14:03:02.000Z",
      "attributes": [
        {
          "key": "http.route",
          "value": {
            "valueType": "string",
            "stringValue": "/"
          }
        }
      ],
      "droppedAttributesCount": 0,
      "events": [
        {
          "time": "2025-07-09T14:03:02.000Z",
          "name": "event1",
          "attributes": [],
          "droppedAttributesCount": 0
        }
      ],
      "droppedEventsCount": 0,
      "links": [
        {
          "traceId": "abcdef0123456789abcdef0123456789",
          "spanId": "abcdefabcdef0102",
          "traceState": "",
          "attributes": [],
          "droppedAttributesCount": 0
        }
      ],
      "droppedLinksCount": 0,
      "status": {
        "message": "status message",
        "code": "ok"
      },
      "resource": {
        "attributes": [],
        "droppedAttributesCount": 0
      },
      "scope": {
        "name": "my-library",
        "version": "1.0.0",
        "attributes": [],
        "droppedAttributesCount": 0
      }
    }
  ]
}
```

The trace detail object has the following keys.

| KEY      | TYPE           | DESCRIPTION                                    |
| -------- | ------         | -----------                                    |
| `spans`  | *array*        | The array of spans contained in the trace               |

Span objects have the following keys.

| KEY                      | TYPE     | DESCRIPTION                                   |
| --------                 | ------   | -----------                                   |
| `traceId`                | *string* | Trace ID                                |
| `spanId`                 | *string* | Span ID                                  |
| `traceState`             | *string* | TraceState of W3C Trace Context                 |
| `parentSpanId`           | *string* | [optional] Parent span ID. Omitted for root spans |
| `name`                   | *string* | Span name                                    |
| `kind`                   | *string* | String of span kind (unspecified, internal, server, client producer, consumer) |
| `startTime`              | *string* | Start time of span. `YYYY-mm-ddTHH:MM:SS.sss+Z` |
| `endTime`                | *number* | End time of span. `YYYY-mm-ddTHH:MM:SS.sss+Z` |
| `attributes`             | *array*  | Array of span attributes                            |
| `droppedAttributesCount` | *number* | Number of attributes dropped                      |
| `events`                 | *array*  | Array of span events                        |
| `droppedEventsCount`     | *number* | Number of events dropped                  |
| `links`                  | *array*  | Array of links to other spans                    |
| `droppedLinksCount`      | *number* | Number of links dropped                    |
| `status`                 | *object* | Span execution state                        |
| `resource`               | *object* | Resource information                                |
| `scope`                  | *object* | Scope information                                |

The span attribute object has the following keys.

| KEY      | TYPE       | DESCRIPTION          |
| -------- | ------     | -----------          |
| `key`    | *string*   | Attribute key           |
| `value`  | *AnyValue* | Event name         |

*AnyValue* consists of `valueType`, which indicates the value type, and `-Value`, which indicates the value (except for `empty`). The names and values of the `-Value` keys that can be used vary depending on the value of the `valueType` key.

| Value of valueType | KEY          | TYPE       |
| --------    | ------     | --------      |
| **string** | `stringValue` | *string*    |
| **bool**   | `boolValue`   | *bool*      |
| **int**    | `intValue`    | *int*       |
| **double** | `doubleValue` | *double*    |
| **array**  | `arrayValue`  | *array*     |
| **kvlist** | `kvlistValue` | *kvlist*    |
| **bytes**  | `bytesValue`  | *bytes*     |
| **empty**  | -             | -           |

*array* contains other values. For instance, it has the following structure.

```json
{
  "valueType": "array",
  "arrayValue": [
    {"valueType": "int", "intValue": 10},
    {"valueType": "int", "intValue": 20}
  ]
}
```

*kvlist* contains other values. For instance, it has the following structure.

```json
{
  "valueType": "kvlist",
  "kvlistValue": {
    "en": {"valueType": "string", "stringValue": "success"}
  }
}
```

The span event object has the following keys.

| KEY                      | TYPE     | DESCRIPTION                                     |
| --------                 | ------   | -----------                                     |
| `time`                   | *string* | Event occurrence time. `YYYY-mm-ddTHH:MM:SS.sss+Z` |
| `name`                   | *string* | Event name                                    |
| `attributes`             | *array*  | Array of event attributes                            |
| `droppedAttributesCount` | *number* | Number of attributes dropped                        |

Link objects to other spans have the following keys.

| KEY                      | TYPE     | DESCRIPTION                    |
| --------                 | ------   | -----------                    |
| `traceId`                | *string* | Trace ID of link destination         |
| `spanId`                 | *string* | Span ID of link destination           |
| `traceState`             | *string* | traceState of W3C Trace Context  |
| `attributes`             | *array*  | Array of link attributes             |
| `droppedAttributesCount` | *number* | Number of link attributes dropped |

The span execution state object has the following keys.

| KEY       | TYPE     | DESCRIPTION                                |
| --------  | ------   | -----------                                |
| `message` | *string* | Status message                     |
| `code`    | *string* | Status code string (unset, ok, error) |

The resource information object has the following keys.

| KEY                      | TYPE     | DESCRIPTION                      |
| --------                 | ------   | -----------                      |
| `attributes`             | *array*  | Array of resource attributes             |
| `droppedAttributesCount` | *number* | Number of resource attributes dropped |

The scope information object has the following keys.

| KEY                      | TYPE     | DESCRIPTION                      |
| --------                 | ------   | -----------                      |
| `name`                   | *string* | Scope name                     |
| `version`                | *string* | Scope version             |
| `attributes`             | *array*  | Array of scope attributes             |
| `droppedAttributesCount` | *number* | Number of scope attributes dropped |

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>when the request is invalid</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when permission to access the specified trace is not granted</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the trace with the specified ID can't be found</td>
    </tr>
  </tbody>
</table>
