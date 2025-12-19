---
Title: Traces
Date: 2025-07-24T12:50:00+09:00
URL: https://mackerel.io/api-docs/entry/traces
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/6802418398528132533
---

<ul class="internal-nav">
  <li><a href="#list">List Traces</a></li>
  <li><a href="#get">Get Trace</a></li>
</ul>

<h2 id="list">List Traces</h2>

Retrieve a list of traces based on specified conditions.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/traces</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input

| KEY                    | TYPE             | DESCRIPTION                                                                 |
| ---------------------- | ---------------- | --------------------------------------------------------------------------- |
| `serviceName`          | *string*         | Service name                                                                |
| `serviceNamespace`     | *string*         | [optional] Service namespace                                                |
| `from`                 | *number*         | Trace search start time (epoch seconds)                               |
| `to`                   | *number*         | Trace search end time (epoch seconds)                                 |
| `environment`          | *string*         | [optional] Environment name                                                 |
| `traceId`              | *string*         | [optional] Trace ID (32-digit hexadecimal string)                          |
| `spanName`             | *string*         | [optional] Span name                                                        |
| `version`              | *string*         | [optional] Version                                                          |
| `issueFingerprint`     | *string*         | [optional] Issue fingerprint                                                |
| `statusCode`           | *string*         | [optional] Code for Span's status. `OK` or `ERROR`                     |
| `minLatencyMillis`     | *number*         | [optional] Minimum latency (milliseconds)                                   |
| `maxLatencyMillis`     | *number*         | [optional] Maximum latency (milliseconds)                                   |
| `attributes`           | *array[object]*  | [optional] List of attribute filter conditions                              |
| `resourceAttributes`   | *array[object]*  | [optional] List of resource attribute filter conditions                     |
| `page`                 | *number*         | [optional] Page number (starts from 1). Default is 1                       |
| `perPage`              | *number*         | [optional] Number of items per page (1-100). Default is 20                 |
| `order`                | *object*         | [optional] Sort condition                                                   |

Attribute filter objects have the following keys.

| KEY        | TYPE     | DESCRIPTION                                                                                     |
| ---------- | -------- | ----------------------------------------------------------------------------------------------- |
| `key`      | *string* | Attribute key                                                                                   |
| `value`    | *string* | Attribute value (specified as string)                                                          |
| `operator` | *string* | Comparison operator. One of `EQ`, `NEQ`, `GT`, `GTE`, `LT`, `LTE`, `STARTS_WITH`        |
| `type`     | *string* | Attribute value type. One of `string`, `int`, `double`, `bool`                                 |

The available `operator` values vary depending on the attribute `type`.

| operator      | string | int | double | bool |
| ------------- | ------ | --- | ------ | ---- |
| `EQ`          | ✓      | ✓   | ✓      | ✓    |
| `NEQ`         | ✓      |     |        | ✓    |
| `GT`          |        | ✓   | ✓      |      |
| `GTE`         |        | ✓   | ✓      |      |
| `LT`          |        | ✓   | ✓      |      |
| `LTE`         |        | ✓   | ✓      |      |
| `STARTS_WITH` | ✓      |     |        |      |

Sort condition objects have the following keys.

| KEY         | TYPE     | DESCRIPTION                                                       |
| ----------- | -------- | ----------------------------------------------------------------- |
| `column`    | *string* | [optional] Sort column. `LATENCY` or `START_AT`. Default is `START_AT` |
| `direction` | *string* | [optional] Sort order. `ASC` or `DESC`. Default is `DESC`               |

#### Input example

```json
{
  "serviceName": "shoppingcart",
  "serviceNamespace": "shop",
  "from": 1718802000,
  "to": 1718888400,
  "environment": "production",
  "statusCode": "ERROR",
  "minLatencyMillis": 1000,
  "maxLatencyMillis": 5000,
  "attributes": [
    {
      "key": "http.status_code",
      "value": "500",
      "operator": "EQ",
      "type": "int"
    },
    {
      "key": "http.method",
      "value": "GET",
      "operator": "EQ",
      "type": "string"
    }
  ],
  "resourceAttributes": [
    {
      "key": "host.name",
      "value": "server",
      "operator": "CONTAINS",
      "type": "string"
    }
  ],
  "page": 1,
  "perPage": 20,
  "order": {
    "column": "START_AT",
    "direction": "DESC"
  }
}
```

### Response

#### Success

```json
{
  "results": [
    {
      "traceId": "550e8400e29b41d4a716446655440000",
      "serviceName": "shoppingcart",
      "serviceNamespace": "shop",
      "environment": "production",
      "title": "GET /api/users",
      "traceStartAt": 1718802000,
      "traceLatencyMillis": 1234,
      "serviceStartAt": 1718802100,
      "serviceLatencyMillis": 567
    }
  ],
  "hasNextPage": true
}
```

The response has the following keys.

| KEY           | TYPE      | DESCRIPTION                            |
| ------------- | --------- | -------------------------------------- |
| `results`     | *array*   | List of traces                         |
| `hasNextPage` | *boolean* | Whether the next page exists           |

Trace objects have the following keys.

| KEY                     | TYPE     | DESCRIPTION                                         |
| ----------------------- | -------- | --------------------------------------------------- |
| `traceId`               | *string* | Trace ID (32-digit hexadecimal string)             |
| `serviceName`           | *string* | Service name                                        |
| `serviceNamespace`      | *string* | Service namespace                                   |
| `environment`           | *string* | Environment name                                    |
| `title`                 | *string* | Trace title (such as root span name)               |
| `traceStartAt`          | *number* | Trace start time (epoch seconds)              |
| `traceLatencyMillis`    | *number* | Overall trace latency (milliseconds)               |
| `serviceStartAt`        | *number* | Service span start time (epoch seconds)       |
| `serviceLatencyMillis`  | *number* | Service span latency (milliseconds)                |

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
      <td>401</td>
      <td>when the API key is invalid</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when accessed from outside the <a href="https://support.mackerel.io/hc/en/articles/360039701952-Restricting-access-to-your-organization-by-specifying-IP-addresses" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
        <td>429</td>
        <td>when the rate limit is exceeded (1 request per second)</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

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
