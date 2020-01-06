---
Title: Alerts
Date: 2016-03-24T18:48:51+09:00
URL: https://mackerel.io/api-docs/entry/alerts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368377210
---

<ul class="internal-nav">
  <li><a href="#get">Getting Alerts</a></li>
  <li><a href="#close">Closing Alerts</a></li>
</ul>


<h2 id="get">Getting Alerts</h2>

This will get a list of the alerts.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alerts</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameters)

You can filter alerts with the following parameters. If `withClosed` is not specified, only open alerts are returned.
If `nextId` is not specified, alerts will be returned in order of latest occurrence time.

| PARAM     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `withClosed` | *boolean* | [optional] Whether or not to get resolved alerts. If true, resolved alerts as well as open alerts are retrieved. |
| `nextId` | *string* | [optional] If `nextId` is specified, alerts older than the id of the specified alert are retrieved. |
| `limit` | *number* | [optional] The maximum number of alerts to retrieve. When omitted, up to 100 cases are retrieved. The most that can be specified is 100. |


### Response

```json
{
  "alerts": [<alert>, <alert>, ...],
  "nextId": xxx
}
```

Alerts will be in chronological order of when they were generated from newest to oldest. `nextId` is retrieved when there are more alerts.

`<alert>`: an object that holds the following keys.

| KEY      | TYPE            | DESCRIPTION                                       |
| -------- | ------          | -----------                                       |
| `id`     | *string*        | alert's ID                                      |
| `status` | *string*        | alert's current status: `"OK"`, `"CRITICAL"`, `"WARNING"`, or `"UNKNOWN"` |
| `monitorId`  | *string* | ID of the monitor that generated the alert |
| `type`  | *string* | the type of monitor: connectivity (`"connectivity"`), host metric (`"host"`), service metric (`"service"`), external monitor (`"external"`), check monitor (`"check"`), expression monitor (`"expression"`), or anomaly detection for roles (`"anomalyDetection"`) |
| `hostId`  | *string* | [optional] the associated host ID. only exists when the type of monitor is either `"connectivity"`, `"host"`, `"check"`, or `"anomalyDetection"` |
| `value`  | *number* | [optional] value of the monitoring target. only exists when the type of monitor is `"host"`, or `"service"` or when there is a reponse time configuration in `"external"` |
| `message`  | *string* | [optional] monitoring target's message. only exists when the type of monitor is either `"check"` or `"external"` |
| `reason`  | *string* | [optional] reason that the alert was closed. does not exist if the alert is unresolved. |
| `openedAt`  | *number* | time stamp when the alert was generated (unix time) |
| `closedAt`  | *number* | [optional] time stamp when the alert was resolved (unix time). only exists if the alert is already resolved |

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
      <td>when `limit` value is larger than maximum allowed value(100)</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="close">Closing Alerts</h2>

The designated alert will be closed.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/close</code> 
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

```javascript
{
  "reason": <text>
}
```

Any text can be appended in the `reason` field. This field is a required item.

### Response

#### Success

A post-update alert will be returned.

```javascript
{
  "id": "<alertId>",
  "status": "OK",
  ...
  "reason": <reason>,
  "opendAt": <opendAt>,
  "closedAt": <closedAt>
}
```

This is the same as the `<alert>` object in [Getting Alerts](#get). `reason` and `closedAt` exist.

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
      <td>when the <code><em>&lt;alertId&gt;</em></code>'s corresponding alert can't be found</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>
