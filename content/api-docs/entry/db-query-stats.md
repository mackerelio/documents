---
Title: Database Query Statistics
Date: 2025-11-04T00:00:00+09:00
URL: https://mackerel.io/api-docs/entry/db-query-stats
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/17179246901316687628
---

<ul class="internal-nav">
  <li><a href="#list">Get Database Query Statistics</a></li>
</ul>

<h2 id="list">Get Database Query Statistics</h2>

Get database query statistics that match the specified conditions from data posted via the tracing feature.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/db-query-stats</code>
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
| `query`            | *string*  | [optional] Filter by SQL query (partial match). Corresponds to `db.query.text` or `db.statement` in OpenTelemetry semantic conventions. The newer specification `db.query.text` is preferred. |
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
      "query": "INSERT INTO orders (user_id, product_id) VALUES (?, ?)",
      "executionCount": 450,
      "totalMillis": 4500.0,
      "averageMillis": 10.0,
      "approxP95Millis": 25.5
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

| KEY               | TYPE     | DESCRIPTION                                   |
| ----------------- | -------- | --------------------------------------------- |
| `query`           | *string* | SQL query string. Corresponds to `db.query.text` or `db.statement` in OpenTelemetry semantic conventions. |
| `executionCount`  | *number* | Number of spans that executed this query     |
| `totalMillis`     | *number* | Total latency (milliseconds). Calculated from span duration (end - start) |
| `averageMillis`   | *number* | Average latency (milliseconds). Average value calculated by dividing total execution time by execution count |
| `approxP95Millis` | *number* | Approximate P95 latency (milliseconds)        |

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
