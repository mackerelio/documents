---
Title: Host metrics
Date: 2016-03-24T18:22:17+09:00
URL: https://mackerel.io/api-docs/entry/host-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368374213
---

<ul class="internal-nav">
  <li><a href="#post">Post Metrics</a></li>
  <li><a href="#get">Get Host Metrics</a></li>
  <li><a href="#get-latest">Get Latest Metrics</a></li>
  <li><a href="#post-graphdef">Post Graph Definitions</a></li>
</ul>


<h2 id="post">Post Metrics</h2>

This will transmit metrics collected by the agent to Mackerel. Recorded values can be checked using web graphs, etc.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/tsdb</code>
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

`<metricValue>` : an object that holds the following keys.

| KEY      | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `hostId` | *string* | host ID (given by the server when the host was registered) |
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
      <td>other than 200</td>
      <td>any error other than the above</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">Get Host Metrics</h2>

This will get metric values that have been posted by the agent.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metrics</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameter)

With the following parameter, the metric name and time span to be collected will be assigned.

| KEY     | TYPE     | DESCRIPTION |
| ------- | -------- | ----------- |
| `name`  | *string* | metric name（`loadavg5`, etc. More info → [metric specs](https://mackerel.io/docs/entry/spec/metrics)） |
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
      <td>when the host or metric doesn't exist</td>
    </tr>
    <tr>
      <td>other than 200</td>
      <td>any error other than the above</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get-latest">Get Latest Metrics</h2>

This will get the latest metrics posted by the agent from each host. 

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/tsdb/latest</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameter)

With the following parameter, host and metric names will be assigned.

| KEY      | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `hostId` | *string* | host ID, multiple assignments possible |
| `name`   | *string* | metric name (`loadavg5` etc. for more info→[Metrics spec.](https://mackerel.io/docs/entry/spec/metrics), multiple assignments possible	 |

### Response

```json
{
  "tsdbLatest": {
    <hostId>: {
      <name>: <metricValue>,
      <name>: <metricValue>,
      …
    },
    <hostId>: {
      …
    },
    …
  }
}
```

`<metricValue>` : an object that holds the following keys.

| KEY      | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `time`   | *number* | unix time |
| `value`  | *number* | measured value at `time` |

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
      <td>other than 200</td>
      <td>any error other than the above</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="post-graphdef">Post Graph Definitions</h2>

This will transmit custom metric graph definitions to Mackerel.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/graph-defs/create</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

```json
[ <graphDef>, <graphDef>, … ]
```
 
`<graphDef>` : an object that contains the following keys.

| KEY           | TYPE     | DESCRIPTION                                    |
| --------------| -------- | ---------------------------------------------- |
| `name`        | *string* | The part of the metric name before the last `.`. Must begin with `custom.`. Additionally, wildcard characters (`*` and `#`) can also be used. |
| `displayName` | *string* | [optional] The graph's display name.                                 |
| `unit`        | *string* | [optional] The type of the graph's values. Possible values are "float", "integer", "percentage", "seconds", "milliseconds", "bytes", "bytes/sec", "bits/sec", and "iops" |
| `metrics`     | *array*  | `[ <metric>, <metric>, ...]` For metrics, refer to the chart below. |


metric: an object that contains the following keys.

| KEY           | TYPE      | DESCRIPTION              |
| ------------- | --------- | ------------------------ |
| `name`        | *string*  | The custom metric name. Must begin with `<graphDef>.name` followed by a `.` and a name (which may contain `[-a-zA-Z0-9_]+` , and wildcard characters  `*` , or `#` ). When using a wildcard, it must be included in the `<graphDef>.name` as well. |
| `displayName` | *string*  | [optional] The metric display name. If omitted, everything after the last `.` in the metric name will be used. By writing `%1` or `%2` , it's possible to use the first and second phrases that match the wildcard characters in the metric name.|
| `isStacked`   | *boolean* | Indicates whether the metrics in question are displayed in stacked format or not. If false, the graph will be displayed in line format. |


### Example Input

```json
[
  {
      "name" : "custom.cpu.foo",
      "displayName": "CPU",
      "unit":"percentage",
      "metrics": [
         { "name": "custom.cpu.foo.user", "displayName": "CPU user", "isStacked": true },
         { "name": "custom.cpu.foo.idle", "displayName": "CPU idle", "isStacked": true },
         ...
      ]
  },
  {
      "name" : "custom.wild.#",
      "displayName": "wildcard",
      "unit":"float",
      "metrics": [
         { "name": "custom.wild.#.foo", "displayName": "wild foo" },
         { "name": "custom.wild.#.bar", "displayName": "wild bar" },
         ...
      ]
  },
  {
    "name": "custom.cpu.bar",
    ...
  }
]
```

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
      <td>other than 200</td>
      <td>any error other than the above</td>
    </tr>
  </tbody>
</table>

<h3 class="annotation">Regarding custom metric names and wildcards</h3>

- Characters that may be used in custom metric names include alphanumeric characters, hyphens (-), underscores (_), and non-consecutive dots (.).
- Custom metric names must begin with `custom.`. (mackerel-agent will automatically attach `custom.`)
- Wildcard characters (`*` or `#`) can be used independently in the names of graph definition metrics either between two dots `.` or after the last dot.
- A wildcard characters will match a sequence of characters excluding dots (i.e. [-a-zA-Z0-9_]+).
- Also, the wildcard `#`, which can be used for making groups in the legend (described later), can only be used once. The whole metric name should look like this `^custom(\.([-a-zA-Z0-9_]+|[*#]))+$`.

If using the wildcard `#`, the graph's legend will be grouped by the portions of the metric names that matched with `#`. For example if there are two definitions `custom.docker.cpu.#.user` and `custom.docker.cpu.#.system`, and six custom metrics

- `custom.docker.cpu.f5240a.user`
- `custom.docker.cpu.f5240a.system`
- `custom.docker.cpu.e866aq.user`
- `custom.docker.cpu.e866aq.system`
- `custom.docker.cpu.e552ad.user`
- `custom.docker.cpu.e552ad.system`

are transmitted, the graph legend will be grouped as shown below.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150909/20150909172654.png" alt="f:id:mackerelio:20150909172654p:plain" title="f:id:mackerelio:20150909172654p:plain" class="hatena-fotolife" itemprop="image"></span></p>

Additionally, if a transmission of custom metrics of a graph definition with wildcards isn't received within a specified time span (roughly 6 to 8 hours) the definition will automatically be deleted, so please use caution.
