---
Title: APM
Date: 2025-11-04T00:00:00+09:00
URL: https://mackerel.io/api-docs/entry/apm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/17179246901317393541
---

<ul class="internal-nav">
  <li><a href="#http-server-stats">Get HTTP Server Statistics</a></li>
  <li><a href="#db-query-stats">Get Database Query Statistics</a></li>
</ul>

<h2 id="http-server-stats">Get HTTP Server Statistics</h2>
Get HTTP server statistics that match the specified conditions from data posted via the tracing feature. Only SERVER-side HTTP requests are aggregated, and CLIENT spans are excluded.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/apm/http-server-stats</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input

This API accepts query parameters to specify conditions. The following parameters are available:

| PARAM NAME         | TYPE      | DESCRIPTION                                                                                                                |
| ------------------ | --------- | -------------------------------------------------------------------------------------------------------------------------- |
| `serviceName`      | *string*  | [required] Service name. Corresponds to `service.name` in OpenTelemetry semantic conventions.                              |
| `serviceNamespace` | *string*  | [optional] Service namespace. Corresponds to `service.namespace` in OpenTelemetry semantic conventions.                    |
| `from`             | *number*  | [required] Start time for retrieving statistics (Unix epoch seconds). The range between `to` is limited to 30 minutes.     |
| `to`               | *number*  | [required] End time for retrieving statistics (Unix epoch seconds). The range between `from` is limited to 30 minutes.     |
| `environment`      | *string*  | [optional] Environment name. Corresponds to `deployment.environment` or `deployment.environment.name` in OpenTelemetry semantic conventions. |
| `version`          | *string*  | [optional] Version. Corresponds to `service.version` in OpenTelemetry semantic conventions.                                |
| `method`           | *string*  | [optional] Filter by HTTP method. Corresponds to `http.request.method` or `http.method` in OpenTelemetry semantic conventions. `http.request.method` is preferred. |
| `route`            | *string*  | [optional] Filter by route (partial match). Corresponds to `http.route`, `url.path`, `http.target`, or `http.url` in OpenTelemetry semantic conventions. Preferred in this order. |
| `orderColumn`      | *string*  | [optional] Sort column. One of `SUM` (total latency), `AVERAGE` (average latency), `P95` (95th percentile latency), `ERROR_RATE` (error rate), or `REQUEST_COUNT` (request count). Default is `P95`. |
| `orderDirection`   | *string*  | [optional] Sort order. Either `ASC` (ascending) or `DESC` (descending). Default is `DESC`.                                 |
| `page`             | *number*  | [optional] Page number (starting from 1). Default is `1`.                                                                  |
| `perPage`          | *number*  | [optional] Number of items per page (1-100). Default is `20`.                                                              |

### Response

#### Success

```json
{
  "results": [
    {
      "method": "GET",
      "route": "/api/users",
      "totalMillis": 837.0,
      "averageMillis": 9.01,
      "approxP95Millis": 19.89,
      "errorRatePercentage": 0.0,
      "requestCount": 93
    },
    {
      "method": "POST",
      "route": "/api/orders",
      "totalMillis": 1250.0,
      "averageMillis": 12.5,
      "approxP95Millis": 25.5,
      "errorRatePercentage": 2.5,
      "requestCount": 100
    }
  ],
  "hasNextPage": false
}
```

The response object has the following keys:

| KEY           | TYPE      | DESCRIPTION                                |
| ------------- | --------- | ------------------------------------------ |
| `results`     | *array*   | Array of HTTP server statistics           |
| `hasNextPage` | *boolean* | Whether the next page exists               |

HTTP server statistics objects have the following keys:

| KEY                    | TYPE     | DESCRIPTION                                   |
| ---------------------- | -------- | --------------------------------------------- |
| `method`               | *string* | HTTP method. Corresponds to `http.request.method` or `http.method` in OpenTelemetry semantic conventions. `http.request.method` is preferred. |
| `route`                | *string* | HTTP route. Corresponds to `http.route`, `url.path`, `http.target`, or `http.url` in OpenTelemetry semantic conventions. Preferred in this order. |
| `totalMillis`          | *number* | Total latency (milliseconds). Calculated from span duration (end - start) |
| `averageMillis`        | *number* | Average latency (milliseconds). Average value calculated by dividing total execution time by request count |
| `approxP95Millis`      | *number* | Approximate P95 latency (milliseconds)        |
| `errorRatePercentage`  | *number* | Error rate (%). Percentage of requests with HTTP status code 500 or higher |
| `requestCount`         | *number* | Request count. Number of spans that handled HTTP requests |

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
      <td>when the input is in an unacceptable format</td>
    </tr>
    <tr>
      <td>401</td>
      <td>when the API key is invalid</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing a service that you don't have access to</td>
    </tr>
    <tr>
      <td>429</td>
      <td>when the API request rate limit is exceeded (1 req/sec)</td>
    </tr>
  </tbody>
</table>

<h2 id="db-query-stats">Get Database Query Statistics</h2>

Get database query statistics that match the specified conditions from data posted via the tracing feature. Only CLIENT spans indicating database access are aggregated, and SERVER spans are excluded.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/apm/db-query-stats</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input

This API accepts query parameters to specify conditions. The following parameters are available:

| PARAM NAME         | TYPE      | DESCRIPTION                                                                                                                |
| ------------------ | --------- | -------------------------------------------------------------------------------------------------------------------------- |
| `serviceName`      | *string*  | [required] Service name. Corresponds to `service.name` in OpenTelemetry semantic conventions.                              |
| `serviceNamespace` | *string*  | [optional] Service namespace. Corresponds to `service.namespace` in OpenTelemetry semantic conventions.                    |
| `from`             | *number*  | [required] Start time for retrieving statistics (Unix epoch seconds). The range between `to` is limited to 30 minutes.     |
| `to`               | *number*  | [required] End time for retrieving statistics (Unix epoch seconds). The range between `from` is limited to 30 minutes.     |
| `environment`      | *string*  | [optional] Environment name. Corresponds to `deployment.environment` or `deployment.environment.name` in OpenTelemetry semantic conventions. |
| `version`          | *string*  | [optional] Version. Corresponds to `service.version` in OpenTelemetry semantic conventions.                                |
| `query`            | *string*  | [optional] Filter by SQL query (partial match). Corresponds to `db.query.text` or `db.statement` in OpenTelemetry semantic conventions. `db.query.text` is preferred. |
| `orderColumn`      | *string*  | [optional] Sort column. One of `SUM` (total latency), `AVERAGE` (average latency), `P95` (95th percentile latency), or `EXECUTION_COUNT` (execution count). Default is `P95`. |
| `orderDirection`   | *string*  | [optional] Sort order. Either `ASC` (ascending) or `DESC` (descending). Default is `DESC`.                                 |
| `page`             | *number*  | [optional] Page number (starting from 1). Default is `1`.                                                                  |
| `perPage`          | *number*  | [optional] Number of items per page (1-100). Default is `20`.                                                              |

### Response

#### Success

```json
{
  "results": [
    {
      "query": "SELECT * FROM users WHERE id = ?",
      "executionCount": 1250,
      "totalMillis": 11262.5,
      "averageMillis": 9.01,
      "approxP95Millis": 19.89
    },
    {
      "query": "INSERT INTO orders (user_id, total) VALUES (?, ?)",
      "executionCount": 850,
      "totalMillis": 6375.0,
      "averageMillis": 7.5,
      "approxP95Millis": 15.2
    }
  ],
  "hasNextPage": false
}
```

The response object has the following keys:

| KEY           | TYPE      | DESCRIPTION                                |
| ------------- | --------- | ------------------------------------------ |
| `results`     | *array*   | Array of database query statistics        |
| `hasNextPage` | *boolean* | Whether the next page exists               |

Database query statistics objects have the following keys:

| KEY                    | TYPE     | DESCRIPTION                                   |
| ---------------------- | -------- | --------------------------------------------- |
| `query`                | *string* | SQL query string. Corresponds to `db.query.text` or `db.statement` in OpenTelemetry semantic conventions. `db.query.text` is preferred. |
| `executionCount`       | *number* | Execution count. Number of spans that executed the query |
| `totalMillis`          | *number* | Total latency (milliseconds). Calculated from span duration (end - start) |
| `averageMillis`        | *number* | Average latency (milliseconds). Average value calculated by dividing total execution time by execution count |
| `approxP95Millis`      | *number* | Approximate P95 latency (milliseconds)        |

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
      <td>when the input is in an unacceptable format</td>
    </tr>
    <tr>
      <td>401</td>
      <td>when the API key is invalid</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing a service that you don't have access to</td>
    </tr>
    <tr>
      <td>429</td>
      <td>when the API request rate limit is exceeded (1 req/sec)</td>
    </tr>
  </tbody>
</table>
