---
Title: Alerts
Date: 2016-03-24T18:48:51+09:00
URL: https://mackerel.io/api-docs/entry/alerts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368377210
---

<ul class="internal-nav">
  <li><a href="#list">List Alerts</a></li>
  <li><a href="#get">Get Alert</a></li>
  <li><a href="#update">Update Alert</a></li>
  <li><a href="#close">Close Alerts</a></li>
  <li><a href="#logs">Get Alert Logs</a></li>
</ul>


<h2 id="list">List Alerts</h2>

This will get the list of alerts.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alerts</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameters)

You can filter alerts with the following parameters. If `withClosed` is not specified, only open alerts are returned.
If `nextId` is not specified, alerts will be returned in order of latest occurrence time.

| PARAM     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `withClosed` | *boolean* | [optional] Whether or not to get resolved alerts. If true, resolved alerts as well as open alerts are retrieved. |
| `nextId` | *string* | [optional] If `nextId` is specified, alerts older than the alert with the specified id are retrieved. |
| `limit` | *number* | [optional] The maximum number of alerts to retrieve. When omitted, up to 100 cases are retrieved. The most that can be specified is 100. |


### Response

```json
{
  "alerts": [<alert>, <alert>, ...],
  "nextId": xxx
}
```

Alerts will be returned in chronological order of when they were generated from newest to oldest. The `nextId` field is returned only when there are more alerts existing.

`<alert>`: an object that holds the following keys.

| KEY      | TYPE            | DESCRIPTION                                       |
| -------- | ------          | -----------                                       |
| `id`     | *string*        | the alert's ID                                      |
| `status` | *string*        | the alert's current status: `"OK"`, `"CRITICAL"`, `"WARNING"`, or `"UNKNOWN"` |
| `monitorId`  | *string* | the ID of the monitor that generated the alert |
| `type`  | *string* | the type of the monitor: connectivity (`"connectivity"`), host metric (`"host"`), service metric (`"service"`), external monitor (`"external"`), check monitor (`"check"`), expression monitor (`"expression"`), anomaly detection for roles (`"anomalyDetection"`), or query monitor (`"query"`) |
| `hostId`  | *string* | [optional] the associated host ID. only exists when the type of the monitor is either `"connectivity"`, `"host"`, `"check"`, or `"anomalyDetection"` |
| `series` | *object* | [optional] the monitoring series. only exists when the type of the monitor is `"query"` [*1](#series)|
| `value`  | *number* | [optional] the value of the monitoring target. only exists when the type of the monitor is `"host"`, `"service"` or `"query"`, or when there is a response time configuration in `"external"` |
| `message`  | *string* | [optional] the monitoring target's message. only exists when the type of the monitor is either `"check"` or `"external"` |
| `reason`  | *string* | [optional] the reason the alert closed. does not exist if the alert is unresolved. |
| `openedAt`  | *number* | the timestamp of when the alert was generated (Unix time) |
| `closedAt`  | *number* | [optional] the timestamp of when the alert was resolved (Unix time). only exists if the alert is already resolved |

<h4 id="series">*1 series</h4>

<span class="table-code">series</span> is an object that holds the following keys.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | the name of the monitoring series, e.g. "container.cpu.utilization" |
| `labels` | *object* | the labels of the monitoring series. an object consisting of keys and a values, where the keys and values of each label are stored, e.g. `{"ip":"127.0.0.1"}` |


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
      <td>when `limit` value is larger than the maximum allowed value (100)</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">Get Alert</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

The alert is returned. The response format is the same as that which can be obtained with [List Alerts](#list).

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
      <td>when the specified alert does not exist</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">Update Alert</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

```json
{
  "memo": "<text>"
}
```

Any text can be appended in the `memo` field. This field is a required item.

### Response

#### Success

alert's ID and memo are returned.

```json
  "id": "<alertId>",
  "memo": "<text>"
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
      <td>404</td>
      <td>when the specified alert does not exist</td>
    </tr>
    <tr>
      <td>413</td>
      <td>when the memo exceeds 80KB</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="close">Close Alerts</h2>

The designated alert will be closed.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/close</code> 
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

```json
{
  "reason": "<text>"
}
```

Any text can be appended in the `reason` field. This field is a required item.

### Response

#### Success

A post-update alert will be returned.

```json
{
  "id": "<alertId>",
  "status": "OK",
  ...
  "reason": "<reason>",
  "openedAt": <openedAt>,
  "closedAt": <closedAt>
}
```

The response object includes the same keys as the `<alert>` object in [Getting Alerts](#get). Here, the `reason` and `closedAt` fields are not optional, they will always exist.

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
      <td>when the alert with the corresponding <code><em>&lt;alertId&gt;</em></code> can't be found</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>


----------------------------------------------

<h2 id="logs">Get Alert Logs</h2>

This will get the list of alert logs. 

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/logs</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameters)

If `nextId` is not specified, alert logs will be returned in order of latest occurrence time.

| PARAM     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `nextId` | *string* | [optional] If `nextId` is specified, alert logs older than the specified are retrieved. |
| `limit` | *number* | [optional] The maximum number of alert logs to retrieve. When omitted, up to 100 logs are retrieved. The most that can be specified is 100. |


### Response

#### Success

```json
{
  "logs": [<log>, <log>, ...],
  "nextId": xxx
}
```

The logs will be returned in order of occurrence time. `nextId` is returned when there are more than `limit` alerts.

<i>`<log>`</i>: an object that holds the following keys. 

| KEY      | TYPE            | DESCRIPTION                                       |
| -------- | ------          | -----------                                       |
| `id`     | *string*        | the alert log's ID                                      |
| `status` | *string*        | the status of the alert at the time the log was generated:  `"OK"`, `"CRITICAL"`, `"WARNING"`, or `"UNKNOWN"`. |
| `trigger`  | *string* | the type of reason why the log was created: monitoring rule (`"monitoring"`), manual (`"manual"`), monitoring rule deletion (`"monitorDelete"`), host retirement (`"hostRetire"`), service deletion (`"serviceDelete"`), manual retraining of anomaly detection for roles (`"anomalyDetectionManuallyRetraining"`), or change of service linked to external monitoring (`"externalMonitoringServiceChange"`). |
| `monitorId`  | *string* / *null* | the ID of the monitoring rule that created the log. it will be `null` if the monitoring rule has been changed or deleted at the time of the request. |
| `targetValue` | *number* / *null* | the observed value. if the alert is not from metric monitoring, it will be null. |
| `statusDetail`  | *object* / *null* | detailed state of the monitored target. it only exists for check monitoring. [*1](#statusDetail) |
| `createdAt`  | *number* | the creation date of the log (Unix time).|

<h4 id="statusDetail">*1 statusDetail</h4>

<i>`statusDetail`</i>: an object that holds the following keys.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `type` | *string* | The type of monitoring rule. It will always be `check`. |
| `detail` | *object* | Detailed information about the state. [*2](#statusDetail-detail)|

<h4 id="statusDetail-detail">*2 detail</h4>

<i>`detail`</i>: an object that holds the following keys.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `message` | *string* | The result of the check monitoring posted to Mackerel. |
| `memo` | *string* | The memo of the monitoring rule. |

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
      <td>when <code>limit</code> value is larger than the maximum allowed value (100)</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified alert does not exist</td>
    </tr>
  </tbody>
</table>
