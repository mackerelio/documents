---
Title: Service metrics
Date: 2016-03-24T18:24:28+09:00
URL: https://mackerel.io/api-docs/entry/service-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368374392
---

<ul class="internal-nav">
  <li><a href="#post">Post Service Metrics</a></li>
  <li><a href="#get">Get Service Metrics</a></li>
  <li><a href="#delete-graph-def">Delete Service Metrics Graph Definition</a></li>
</ul>


<h2 id="post">Post Service Metrics</h2>

This will transmit metrics tied to services to Mackerel.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/tsdb</code>
</p>

If old values are being transmitted to the API, the values on the Mackerel interface will be overwritten. If posting with a timestamp older than 24 hours, those metrics will not be recorded (a status code 200 response will be returned). Additionally, an alert will not occur for an old value, even if a threshold is surpassed, since alerts occur based on the latest values from the Mackerel interface.

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

```json
[ <metricValue>, <metricValue>, … ]
```

`<metricValue>` : an object that holds the following keys

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name`   | *string* | metric name（`[a-zA-Z0-9._-]+`） |
| `time`   | *number* | unix time |
| `value`  | *number* | measured value at `time` |

### Response

#### Success

```json
{
  "success": true
}
```

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
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>429</td>
      <td>When the number of requests per minute is exceeded. Correct this by setting the posting frequency to a 1 minute interval, or posting multiple metrics at once, etc.</td>
    </tr>
    <tr>
      <td>other than 200</td>
      <td>any error other than the above</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">Get Service Metrics</h2>

This will get metric values connected to services.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metrics</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameter)

With the following parameter, the metric name and time span to be collected will be assigned.

| KEY     | TYPE     | DESCRIPTION |
| ------- | -------- | ----------- |
| `name`  | *string* | metric name |
| `from`  | *number* | the start of the time period you want metrics for (unix time) |
| `to`    | *number* | the end of the time period you want metrics for (unix time) |

### Response

#### Success

```json
{
  "metrics": [
    {
      "time": <time>,
      "value": <value>
    },
    {
      "time": <time>,
      "value": <value>
    },
    …
  }
}
```

- response will be in chronological order
- the `<time>` displayed will be in unix time

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
      <td>404</td>
      <td>when the service or metric doesn't exist</td>
    </tr>
    <tr>
      <td>other than 200</td>
      <td>any error other than the above</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete-graph-def">Delete Service Metrics Graph Definition</h2>

This will remove a graph definition for service metrics from Mackerel. The graph will be regenerated when the service metrics are sent again.

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/graph-defs/<em>&lt;graphName&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY     | TYPE     | DESCRIPTION |
| ------- | -------- | ----------- |
| `serviceName`  | *string* | service name |
| `graphName`  | *string* | graph name to delete (ends with `*`) |

### Response

#### Success

```json
{
  "success": true
}
```

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
      <td>404</td>
      <td>when the graph definition or service does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>
