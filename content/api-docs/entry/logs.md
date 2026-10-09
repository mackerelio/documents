---
Title: Logs
Date: 2026-09-17T17:00:00+09:00
URL: https://mackerel.io/api-docs/entry/logs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/14945776032078816660
---

<ul class="internal-nav">
  <li><a href="#list">List Logs</a></li>
  <li><a href="#list-saved-searches">List Saved Log Searches</a></li>
</ul>

<h2 id="list">List Logs</h2>

Retrieve a list of logs based on specified conditions.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/logs</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input

| KEY                | TYPE            | DESCRIPTION                                                                                                                        |
| ------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `serviceName`      | *string*        | Service name                                                                                                                        |
| `serviceNamespace` | *string*        | [optional] Service namespace. Default is an empty string (`""`)                                                                    |
| `from`             | *number*        | Log search start time (Unix epoch seconds). The search range is the half-open interval from `from` (inclusive) to `to` (exclusive) |
| `to`               | *number*        | Log search end time (Unix epoch seconds). A time earlier than `from` can't be specified. The period from `from` to `to` is limited to 30 days |
| `keywords`         | *array[string]* | [optional] List of keywords for filtering the body. Matching is done by partial match against the body, and when multiple keywords are specified, logs containing all of them are returned. When omitted, the body isn't used for filtering. An empty array and an empty string can't be specified |
| `severities`       | *array[string]* | [optional] List of log level filter conditions. Logs matching any of them are returned. One of `UNSPECIFIED`, `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL` |
| `traceId`          | *string*        | [optional] Filter by trace ID (32-digit hexadecimal string)                                                                        |
| `bodyAttributes`   | *array[object]* | [optional] List of filter conditions on structured log fields. When multiple conditions are specified, logs satisfying all of them are returned |
| `order`            | *object*        | [optional] Sort condition                                                                                                          |
| `pageCursor`       | *object*        | [optional] Starting point for paging. When omitted, logs are retrieved from the beginning of the order specified by `order`         |
| `perPage`          | *number*        | [optional] Number of items per page (1-1000). Default is 50                                                                        |

The log levels specified in `severities` are strings that correspond to the [OpenTelemetry SeverityNumber](https://opentelemetry.io/docs/specs/otel/logs/data-model/#field-severitynumber).

Structured log field filter objects have the following keys. Logs whose body isn't JSON, and logs that don't have the field pointed to by `keyPath`, don't match.

| KEY        | TYPE            | DESCRIPTION                                                                              |
| ---------- | --------------- | ------------------------------------------------------------------------------------------ |
| `keyPath`  | *array[string]* | Path to the key to filter on. List the keys in order of nesting                           |
| `value`    | *string*        | Field value (specified as string)                                                        |
| `operator` | *string*        | Comparison operator. One of `EQ`, `NEQ`, `GT`, `GTE`, `LT`, `LTE`, `STARTS_WITH`          |
| `type`     | *string*        | Field value type. One of `string`, `int`, `double`, `bool`                                |

For a nested key (`{"req": {"method": ...}}`), specify `keyPath` as `["req", "method"]`.

The available `operator` values vary depending on the field value `type`.

| operator      | string | int | double | bool |
| ------------- | ------ | --- | ------ | ---- |
| `EQ`          | ✓      | ✓   | ✓      | ✓    |
| `NEQ`         | ✓      |     |        | ✓    |
| `GT`          |        | ✓   | ✓      |      |
| `GTE`         |        | ✓   | ✓      |      |
| `LT`          |        | ✓   | ✓      |      |
| `LTE`         |        | ✓   | ✓      |      |
| `STARTS_WITH` | ✓      |     |        |      |

`NEQ` returns logs that have the field but whose value doesn't match. Logs that don't have the field don't match.

Sort condition objects have the following keys.

| KEY         | TYPE     | DESCRIPTION                                                        |
| ----------- | -------- | -------------------------------------------------------------------- |
| `column`    | *string* | [optional] Sort column can be `TIMESTAMP`. Default is `TIMESTAMP`    |
| `direction` | *string* | [optional] Sort order. `ASC` or `DESC`. Default is `DESC`            |

Paging cursor objects have the following keys.

| KEY         | TYPE     | DESCRIPTION                                                                                                    |
| ----------- | -------- | ---------------------------------------------------------------------------------------------------------------- |
| `direction` | *string* | Paging direction from `cursor` in the order specified by `order`. `NEXT` retrieves logs after `cursor` (later in the order of `order`), and `PREVIOUS` retrieves logs before `cursor` |
| `cursor`    | *string* | Cursor to start from. Specify the value of `pagination.startCursor` or `pagination.endCursor`. The log specified as the starting point isn't included in the results, and logs are returned starting from the next one in the direction of `direction` |

#### Input example

```json
{
  "serviceName": "shoppingcart",
  "serviceNamespace": "shop",
  "from": 1788220800,
  "to": 1788739200,
  "keywords": ["error"],
  "severities": ["ERROR", "WARN"],
  "traceId": "550e8400e29b41d4a716446655440000",
  "bodyAttributes": [
    {
      "keyPath": ["status_code"],
      "value": "500",
      "operator": "EQ",
      "type": "int"
    },
    {
      "keyPath": ["req", "method"],
      "value": "GET",
      "operator": "EQ",
      "type": "string"
    },
    {
      "keyPath": ["http.target"],
      "value": "/api/v0",
      "operator": "STARTS_WITH",
      "type": "string"
    }
  ],
  "order": {
    "column": "TIMESTAMP",
    "direction": "DESC"
  },
  "pageCursor": {
    "direction": "NEXT",
    "cursor": "cursor-string"
  },
  "perPage": 100
}
```

### Response

#### Success

```json
{
  "results": [
    {
      "cursor": "cursor-string",
      "timestamp": "2026-09-07T12:34:56.789123456Z",
      "severity": "ERROR",
      "severityNumber": 17,
      "body": "request failed",
      "traceId": "550e8400e29b41d4a716446655440000",
      "spanId": "051581bf3cb55c13",
      "serviceName": "shoppingcart",
      "serviceNamespace": "shop",
      "attributes": [
        {
          "key": "http.status_code",
          "value": {
            "valueType": "string",
            "stringValue": "500"
          }
        }
      ],
      "resourceAttributes": [
        {
          "key": "host.name",
          "value": {
            "valueType": "string",
            "stringValue": "server"
          }
        }
      ],
      "scopeAttributes": []
    }
  ],
  "pagination": {
    "hasNextPage": true,
    "hasPreviousPage": false,
    "startCursor": "cursor-string",
    "endCursor": "cursor-string"
  }
}
```

The response has the following keys.

| KEY          | TYPE     | DESCRIPTION                  |
| ------------ | -------- | ---------------------------- |
| `results`    | *array*  | List of log records          |
| `pagination` | *object* | Cursor-based pagination info |

Log record objects have the following keys.

| KEY                  | TYPE     | DESCRIPTION                                                                  |
| -------------------- | -------- | ------------------------------------------------------------------------------ |
| `cursor`             | *string* | Cursor pointing to this log                                                  |
| `timestamp`          | *string* | Time of the log (RFC 3339 format)                                            |
| `severity`           | *string* | Log level                                                                    |
| `severityNumber`     | *number* | OpenTelemetry SeverityNumber                                                 |
| `body`               | *string* | Log body                                                                     |
| `traceId`            | *string* | Related trace ID (32-digit hexadecimal string). `null` when not associated   |
| `spanId`             | *string* | Related span ID (16-digit hexadecimal string). `null` when not associated    |
| `serviceName`        | *string* | Service name                                                                 |
| `serviceNamespace`   | *string* | Service namespace. `null` when not set                                       |
| `attributes`         | *array*  | List of log record attributes                                                |
| `resourceAttributes` | *array*  | List of resource attributes                                                  |
| `scopeAttributes`    | *array*  | List of scope attributes                                                     |

Attribute objects have the following keys.

| KEY     | TYPE     | DESCRIPTION     |
| ------- | -------- | --------------- |
| `key`   | *string* | Attribute key   |
| `value` | *object* | Attribute value |

`value` is an object that consists of `valueType`, which indicates the value type, and the value key corresponding to that type (`-Value`). Currently, `valueType` is only `string`, and the value is stored as a string in `stringValue`.

```json
{
  "valueType": "string",
  "stringValue": "500"
}
```

Pagination info objects have the following keys.

| KEY               | TYPE      | DESCRIPTION                                                                                      |
| ----------------- | --------- | -------------------------------------------------------------------------------------------------- |
| `hasNextPage`     | *boolean* | Whether the next page (the page with `pageCursor.direction` of `NEXT`) exists                     |
| `hasPreviousPage` | *boolean* | Whether the previous page (the page with `pageCursor.direction` of `PREVIOUS`) exists             |
| `startCursor`     | *string*  | Cursor of the first result. Specify it in `pageCursor.cursor` to page in the `PREVIOUS` direction. `null` when the results are empty |
| `endCursor`       | *string*  | Cursor of the last result. Specify it in `pageCursor.cursor` to page in the `NEXT` direction. `null` when the results are empty      |

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
      <td>when the logging feature isn't available for the organization / when access to the logs isn't permitted / when accessed from outside the <a href="https://support.mackerel.io/hc/en/articles/360039701952-Restricting-access-to-your-organization-by-specifying-IP-addresses" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>429</td>
      <td>when the rate limit is exceeded (1 request per second). A <code>Retry-After</code> header with the wait time in seconds before retrying is returned</td>
    </tr>
  </tbody>
</table>

<h2 id="list-saved-searches">List Saved Log Searches</h2>

Retrieve a list of saved log search conditions.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/saved-log-searches</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "results": [
    {
      "id": "1234",
      "name": "For checking error logs",
      "description": null,
      "definition": {
        "serviceName": "shoppingcart",
        "serviceNamespace": "shop",
        "keywords": ["timeout"],
        "severities": ["ERROR", "FATAL"],
        "traceId": null,
        "bodyAttributes": [
          {
            "keyPath": ["status_code"],
            "value": "500",
            "operator": "GTE",
            "type": "int"
          }
        ]
      },
      "createdAt": 1718802000,
      "updatedAt": 1718888400
    }
  ]
}
```

The response has the following keys.

| KEY       | TYPE    | DESCRIPTION                         |
| --------- | ------- | ---------------------------------   |
| `results` | *array* | List of saved log search conditions |

Saved log search condition objects have the following keys.

| KEY           | TYPE     | DESCRIPTION                                                     |
| ------------- | -------- | ------------------------------------------------------------    |
| `id`          | *string* | ID of the saved search condition                                |
| `name`        | *string* | Name of the search condition                                    |
| `description` | *string* | Description of the search condition. `null` when not set        |
| `definition`  | *object* | Content of the search condition                                 |
| `createdAt`   | *number* | Time the search condition was created (Unix epoch seconds)      |
| `updatedAt`   | *number* | Time the search condition was last changed (Unix epoch seconds) |

The search condition content object has the following keys. Each key corresponds to the input parameter of the same name in [List Logs](#list).

| KEY                | TYPE            | DESCRIPTION                                                  |
| ------------------ | --------------- | ------------------------------------------------------------ |
| `serviceName`      | *string*        | Service name                                                 |
| `serviceNamespace` | *string*        | Service namespace. `null` when not set                       |
| `keywords`         | *array[string]* | List of keywords for filtering the body                      |
| `severities`       | *array[string]* | List of log level filter conditions                          |
| `traceId`          | *string*        | Filter by trace ID. `null` when not set                      |
| `bodyAttributes`   | *array[object]* | List of filter conditions on structured log fields           |

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
      <td>401</td>
      <td>when the API key is invalid</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the logging feature isn't available for the organization / when accessed from outside the <a href="https://support.mackerel.io/hc/en/articles/360039701952-Restricting-access-to-your-organization-by-specifying-IP-addresses" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>
