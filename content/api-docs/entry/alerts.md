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
| `type`  | *string* | the type of the monitor: connectivity (`"connectivity"`), host metric (`"host"`), service metric (`"service"`), external monitor (`"external"`), check monitor (`"check"`), expression monitor (`"expression"`), or anomaly detection for roles (`"anomalyDetection"`) |
| `hostId`  | *string* | [optional] the associated host ID. only exists when the type of the monitor is either `"connectivity"`, `"host"`, `"check"`, or `"anomalyDetection"` |
| `value`  | *number* | [optional] the value of the monitoring target. only exists when the type of the monitor is `"host"`, or `"service"` or when there is a reponse time configuration in `"external"` |
| `message`  | *string* | [optional] the monitoring target's message. only exists when the type of the monitor is either `"check"` or `"external"` |
| `reason`  | *string* | [optional] the reason the alert closed. does not exist if the alert is unresolved. |
| `openedAt`  | *number* | the timestamp of when the alert was generated (Unix time) |
| `closedAt`  | *number* | [optional] the timestamp of when the alert was resolved (Unix time). only exists if the alert is already resolved |

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

<h2 id="close">Closing Alerts</h2>

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

```javascript
{
  "reason": "<text>"
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
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>
