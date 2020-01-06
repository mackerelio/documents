---
Title: Monitors
Date: 2016-03-24T18:28:28+09:00
URL: https://mackerel.io/api-docs/entry/monitors
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368374710
---

<ul class="internal-nav">
  <li><a href="#create">Registering Monitor Configurations</a></li>
  <li><a href="#list">Listing Monitor Configurations</a></li>
  <li><a href="#get">Getting Monitor Configurations</a></li>
  <li><a href="#update">Updating Monitor Configurations</a></li>
  <li><a href="#delete">Deleting Monitor Configurations</a></li>
</ul>


<h2 id="create">Registering Monitor Configurations</h2>

Monitors for various types of metrics as well as external monitors will be registered with Mackerel.
The input procedure varies depending on the monitoring target.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/monitors</code>
</p>

### Required permissions for API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

<ul class="internal-nav create">
  <li><a href="#create-host-metric-monitoring">Host metric monitoring</a></li>
  <li><a href="#create-connectivity-monitoring">Host connectivity monitoring</a></li>
  <li><a href="#create-service-metric-monitoring">Service metric monitoring</a></li>
  <li><a href="#create-external-monitoring">External monitoring</a></li>
  <li><a href="#create-expression-monitoring">Expression monitoring</a></li>
  <li><a href="#create-anomaly-detection-monitoring">Monitoring with Anomaly Detection for Roles</a></li>
</ul>

<h3 id="create-host-metric-monitoring">Host metric monitoring</h3>

#### Input (for host metric monitoring)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | constant string `"host"`               |
| `name`          | *string*   | arbitrary name that can be referenced from the monitors list, etc. |
| `memo`          | *string*   | [optional] notes for the monitoring configuration |
| `duration`      | *number*   | average value of the designated interval (in minutes) will be monitored. valid interval (1 to 10 min.) |
| `metric`        | *string*   | name of the host metric targeted by monitoring. by designating a specific constant string, comparative monitoring is possible [*1](#comparative-monitoring) | 
| `operator`      | *string*   | determines the conditions that state whether the designated variable is either big or small. the observed value is on the left of `”>”` or `”<”` and the designated value is on the right | 
| `warning`       | *number*   | the threshold that generates a warning alert |
| `critical`      | *number*   | the threshold that generates a critical alert |
| `maxCheckAttempts`           | *number*   | [optional] number of consecutive Warning/Critical instances before an alert is made. Default setting is 1 (1-10) |
| `notificationInterval`       | *number*   | [optional] the time interval (in minutes) for re-sending notifications. If this field is omitted, notifications will not be re-sent. |
| `scopes`        | *array[string]* | [optional] monitoring target’s service name or role details name  [*2](#service-name) | 
| `excludeScopes` | *array[string]* | [optional] monitoring exclusion target’s service name or role details name  [*2](#service-name) | 
| `isMute` | *boolean* | [optional] Whether monitoring is muted or not [*3](#muted-monitoring) | 


##### Example Input

```json
{
  "type": "host",
  "name": "disk.aa-00.writes.delta",
  "memo": "This monitor is for Hatena Blog.",
  "duration": 3,
  "metric": "disk.aa-00.writes.delta",
  "operator": ">",
  "warning": 20000.0,
  "critical": 400000.0,
  "maxCheckAttempts": 3,
  "notificationInterval": 60,
  "scopes": [
    "Hatena-Blog"
  ],
  "excludeScopes": [
    "Hatena-Bookmark: db-master"
  ]
}
```

#### Response (for host metric monitoring)

##### Success

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "host",
  "name": "disk.aa-00.writes.delta",
  "memo": "This monitor is for Hatena Blog.",
  "duration": 3,
  "metric": "disk.aa-00.writes.delta",
  "operator": ">",
  "warning": 20000.0,
  "critical": 400000.0,
  "maxCheckAttempts": 3,
  "notificationInterval": 60,
  "scopes": [
    "Hatena-Blog"
  ],
  "excludeScopes": [
    "Hatena-Bookmark: db-master"
  ]
}
```

`id` will be given and returned.

##### Error

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>name</code> is empty</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>memo</code> exceeds 250 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>duration</code> is not in the range of 1~10</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>maxCheckAttempts</code> is not in the range of 1~10</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the service name and role name that are assigned to <code>scope</code> and <code>excludeScopes</code> haven’t been registered yet</td>
    </tr>
    <tr>
    <tr>
      <td>400</td>
      <td>when the notification re-sending time interval is not set at 10 minutes or more</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h4 id="comparative-monitoring" class="annotation">*1 comparative monitoring</h4>

If monitoring host metrics, by assigning a specific character string to `metric`, comparative monitoring will be done for that metric. `metric`s that can be assigned as comparative monitoring values are as follows.


| metric             |
|--------------------|
| `"cpu%"`           |
| `"memory%"`        |
| `"disk%"`          |
| `"swap%"`          |

<h4 id="service-name">*2 Service name and Role service name</h4>

Service name as well as role service name are character strings in the format `<service name>` and `<service name>:<role name>`.

<table class="eg-table">
  <tbody>
  <tr>
    <th>e.g.</th>
    <td>If the service name for Hatena-Bookmark is <code>Hatena-Bookmark</code> then the db-master role in the service Hatena-Bookmark would be <code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

Usable characters are `/^[A-Za-z0-9][A-Za-z0-9_-]+$/`.

<h4 id="muted-monitoring">*3 Muted monitoring</h4>

This function disables notifications in monitoring. Alerts occur in response to monitoring thresholds, but notifications will not be sent to notification channels.

<h3 id="create-connectivity-monitoring">Host connectivity monitoring</h3>

#### Input (host connectivity monitoring)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | constant string `"connectivity"`               |
| `name`          | *string*   | [optional] arbitrary name that can be referenced from the monitors list, etc. The default value is `connectivity`. |
| `memo`          | *string*   | [optional] notes for the monitoring configuration |
| `scopes`        | *array[string]* | [optional] The service name or role details name of the monitoring target. [*2](#service-name)  |
| `excludeScopes` | *array[string]* | [optional] The service name or role details name of the monitoring exception. [*2](#service-name)  |
| `notificationInterval` | *number* | [optional] the time interval (in minutes) for re-sending notifications. If this field is omitted, notifications will not be re-sent. |
| `isMute`        | *boolean*       | [optional] whether monitoring is muted or not |

##### Example Input

```json
{
  "type": "connectivity",
  "name": "connectivity service1",
  "memo": "A monitor that checks connectivity.",
  "scopes": [
    "service1"
  ],
  "excludeScopes": [
    "service1: role3"
  ]
}
```

#### Response (Host connectivity monitoring)

##### Success

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "connectivity",
  "name": "connectivity service1",
  "memo": "A monitor that checks connectivity.",
  "scopes": [
    "service1"
  ],
  "excludeScopes": [
    "service1: role3"
  ]
}
```

`id` will be given and returned

##### Error

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>name</code> is empty</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the<code>memo</code>exceeds 250 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the specified service name or role details name is not registered in <code>scope</code> or <code>excludeScopes</code></td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the notification re-sending time interval is not set at 10 minutes or more</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h3 id="create-service-metric-monitoring">Service metric monitoring</h3>

#### Input (when monitoring service metrics)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | constant string `"service"`               |
| `name`          | *string*   | arbitrary name that can be referenced from the monitors list, etc. |
| `memo`          | *string*   | [optional] notes for the monitoring configuration |
| `service`       | *string*   | name of the service targeted by monitoring |
| `duration`      | *number*   | monitors the average value of the designated number of points. range: most recent 1~10 points |
| `metric`        | *string*   | name of the monitoring target’s host metric name |
| `operator`      | *string*   | determines the conditions that state whether the designated variable is either big or small. the observed value is on the left of `”>”` or `”<”` and the designated value is on the right |
| `warning`       | *number*   | the threshold that generates a warning alert |
| `critical`      | *number*   | the threshold that generates a critical alert |
| `maxCheckAttempts`          | *number*   | [optional] number of consecutive Warning/Critical instances before an alert is made. Default setting is 1 (1-10) |
| `missingDurationWarning`    | *number*   | [optional] the threshold (in minutes) to generate a warning alert for interruption monitoring                                                                |
| `missingDurationCritical`   | *number*   | [optional] the threshold (in minutes) to generate a critical alert for interruption monitoring                                                                |
| `notificationInterval`      | *number*   | [optional] the time interval (in minutes) for re-sending notifications. If this field is omitted, notifications will not be re-sent. |
| `isMute` | *boolean* | [optional] Whether monitoring is muted or not [*3](#muted-monitoring) | 

##### Example Input

```json
{
  "type": "service",
  "name": "Hatena-Blog - access_num.4xx_count",
  "memo": "A monitor that checks the number of 4xx for Hatena Blog",
  "service": "Hatena-Blog",
  "duration": 1,
  "metric": "access_num.4xx_count",
  "operator": ">",
  "warning": 50.0,
  "critical": 100.0,
  "maxCheckAttempts": 3,
  "missingDurationWarning": 360,
  "missingDurationCritical": 720,
  "notificationInterval": 60
}
```

#### Response (when monitoring service metrics)

##### Success

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "service",
  "name": "Hatena-Blog - access_num.4xx_count",
  "memo": "A monitor that checks the number of 4xx for Hatena Blog",
  "service": "Hatena-Blog",
  "duration": 1,
  "metric": "access_num.4xx_count",
  "operator": ">",
  "warning": 50.0,
  "critical": 100.0,
  "maxCheckAttempts": 3,
  "missingDurationWarning": 360,
  "missingDurationCritical": 720,
  "notificationInterval": 60
}
```

`id` will be given and returned.

##### Error

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>name</code> is empty</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>memo</code> exceeds 250 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the duration is not in the range of 1~10</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>maxCheckAttempts</code> is not in the range of 1~10</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>missingDurationWarning</code> or <code>missingDurationCritical</code> is not a multiple of 10 minutes, or is more than a week</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the service name assigned to the service hasn’t been registered yet</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the notification re-sending time interval is not set at 10 minutes or more</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h3 id="create-external-monitoring">External monitoring</h3>

#### Input (external monitoring)

| KEY                    | TYPE       | DESCRIPTION                      |
| ---------------------- | ---------- | -------------------------------- |
| `type`                 | *string*   | constant string `"external"`          |
| `name`                 | *string*   | arbitrary name that can be referenced from the monitors list, etc. |
| `memo`                            | *string*   | [optional] notes for the monitoring configuration |
| `url`                  | *string*   | monitoring target URL |
| `method`               | *string*   | [optional] request method, one of `GET`, `POST`, `PUT`, `DELETE`. If omitted, `GET` method is used. |
| `service`              | *string*   | [optional] service name (when response time is monitored, it will be graphed in the service metrics of the service linked here) |
| `notificationInterval` | *number*   | [optional] the time interval (in minutes) for re-sending notifications. If this field is omitted, notifications will not be re-sent. |
| `responseTimeWarning`  | *number*   | [optional] the response time threshold for Warning alerts (in milliseconds) `service` designation is required|
| `responseTimeCritical` | *number*   | [optional] the response time threshold for Critical alerts (in milliseconds) `service` designation is required |
| `responseTimeDuration` | *number*   | [optional] will monitor the avg. value of requests in the designated time frame (1-10 min.). `service` designation is required |  
| `containsString`       | *string*   | [optional] string which should be contained by the response body |  
| `maxCheckAttempts`     | *number*   | [optional] number of consecutive Warning/Critical instances before an alert is made. Default setting is 1 (1-10) |
| `certificationExpirationWarning`    | *number*   | [optional] certification expiration date monitor’s “Warning” threshold. number of days remaining until expiration. |
| `certificationExpirationCritical`   | *number*   | [optional] certification expiration date monitor’s “Critical” threshold. number of days remaining until expiration. |
| `isMute`               | *boolean*  | [optional] Whether monitoring is muted or not [*3](#muted-monitoring) | 
| `headers`              | *array[object]* | [optional] The values that should be configured as the HTTP request header specified by `name` and `value`. If this field is omitted, the default header will be configured. If you do not want to configure headers, specify an empty array. |
| `requestBody`          | *string*   | [optional] HTTP request body |

In order to monitor response time, it's necessary to assign `responseTimeWarning`, `responseTimeCritical`, and `responseTimeDuration`. . In order to monitor the certification expiration date, it’s necessary to assign both `certificationExpirationWarning`, and `certificationExpirationCritical`.

##### Example Input

```json
{
  "type": "external",
  "name": "Example Domain",
  "memo": "Monitors example.com",
  "method": "GET",
  "url": "https://example.com",
  "service": "Hatena-Blog",
  "notificationInterval": 60,
  "responseTimeWarning": 5000,
  "responseTimeCritical": 10000,
  "responseTimeDuration": 3,
  "containsString": "Example",
  "maxCheckAttempts": 3,
  "certificationExpirationWarning": 90,
  "certificationExpirationCritical": 30,
  "isMute": false,
  "headers": [{"name":"Cache-Control", "value":"no-cache"}]
}
```

#### Response (external monitoring)

##### Success

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "external",
  "name": "example.com",
  "memo": "Monitors example.com",
  "method": "GET",
  "url": "https://example.com",
  "service": "Hatena-Blog",
  "notificationInterval": 60,
  "responseTimeWarning": 5000,
  "responseTimeCritical": 10000,
  "responseTimeDuration": 3,
  "containsString": "Example",
  "maxCheckAttempts": 3,
  "certificationLimitWarning": 90,
  "certificationLimitCritical": 30,
  "isMute": false,
  "headers": [{"name":"Cache-Control", "value":"no-cache"}]
}
```

`id` will be given and returned.

##### Error

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>name</code> is empty</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>memo</code> exceeds 250 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>url</code> scheme is not http or https</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the notification re-sending time interval is not set at 10 minutes or more</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>maxCheckAttempts</code> is not in the range of 1~10</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h3 id="create-expression-monitoring">Expression monitoring</h3>

#### Input(expression monitoring)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | constant string `"expression"`               |
| `name`          | *string*   | arbitrary name that can be referenced from the monitors list, etc.   |
| `memo`          | *string*   | [optional] notes for the monitoring configuration |
| `expression`    | *string*   | Expression of the monitoring target. Only valid for graph sequences that become one line. |
| `operator`      | *string*   | determines the conditions that state whether the designated variable is either big or small. the observed value is on the left of ”>”or ”<” and the designated value is on the right|
| `warning`       | *number*   | the threshold that generates a warning alert |
| `critical`      | *number*   | the threshold that generates a critical alert |
| `notificationInterval` | *number* | [optional] The time interval (in minutes) for re-sending notifications. If this field is omitted, notifications will not be re-sent. |
| `isMute`        | *boolean*       | [optional] whether monitoring is muted or not  [*3](#mute) |

##### Input example

```json
{
  "type": "expression",
  "name": "role average",
  "memo": "Monitors the average of loadavg5",
  "expression": "avg(roleSlots(\"server:role\",\"loadavg5\"))",
  "operator": ">",
  "warning": 5.0,
  "critical": 10.0,
  "notificationInterval": 60
}
```

#### Response(expression monitoring)

##### Success

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "expression",
  "name": "role average",
  "memo": "Monitors the average of loadavg5",
  "expression": "avg(roleSlots(\"server:role\",\"loadavg5\"))",
  "operator": ">",
  "warning": 5.0,
  "critical": 10.0,
  "notificationInterval": 60
}
```

`id` will be given and returned.

##### Error

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>name</code> is empty</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>memo</code> exceeds 250 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the notification re-sending time interval is not set at 10 minutes or more</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when an invalid expression is designated</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="list">Listing Monitor Configurations</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/monitors</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "monitors": [
    {
      "id"  : "2cSZzK3XfmB",
      "type": "host",
      "name": "disk.aa-00.writes.delta",
      "memo": "This monitor is for Hatena Blog.",
      "duration": 3,
      "metric": "disk.aa-00.writes.delta",
      "operator": ">",
      "warning": 20000.0,
      "critical": 400000.0,
      "maxCheckAttempts": 3,
      "scopes": [
        "Hatena-Blog"
      ],
      "excludeScopes": [
        "Hatena-Bookmark: db-master"
      ]
    },
    {
      "id": "2cSZzK3XfmA",
      "type": "connectivity",
      "scopes": [],
      "excludeScopes": []
    },
    {
      "id"  : "2cSZzK3XfmC",
      "type": "service",
      "name": "Hatena-Blog - access_num.4xx_count",
      "memo": "A monitor that checks the number of 4xx for Hatena Blog",
      "service": "Hatena-Blog",
      "duration": 1,
      "metric": "access_num.4xx_count",
      "operator": ">",
      "warning": 50.0,
      "critical": 100.0,
      "maxCheckAttempts": 1,
      "notificationInterval": 60
    },
    {
      "id"  : "2cSZzK3XfmD",
      "type": "external",
      "name": "example.com",
      "memo": "Monitors example.com",
      "url": "http://www.example.com",
      "service": "Hatena-Blog",
      "headers": [{"name":"Cache-Control", "value":"no-cache"}],
      "maxCheckAttempts": 1
    },
    {
      "id"  : "2cSZzK3XfmE",
      "type": "expression",
      "name": "role average",
      "memo": "Monitors the average of loadavg5",
      "expression": "avg(roleSlots(\"server:role\",\"loadavg5\"))",
      "operator": ">",
      "warning": 5.0,
      "critical": 10.0,
      "notificationInterval": 60
    }
  ]
}
```

- each field is the same as when the [monitor was created](#create)
- list is ordered as monitor type -> name (same as the list of monitors on mackerel.io)

<h3 id="create-anomaly-detection-monitoring">Monitoring with Anomaly Detection for Roles</h3>

#### Input (when monitoring with Anomaly Detection for Roles)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | constant string `"anomalyDetection"`               |
| `name`          | *string*   | arbitrary name that can be referenced from the monitors list, etc. |
| `memo`          | *string*   | [optional] notes for the monitoring configuration |
| `scopes`        | *array[string]* | [optional] monitoring target’s service name and role details name  [*2](#service-name) | 
| `warningSensitivity`       | *string*   | the sensitivity (`insensitive`, `normal`, or `sensitive`) that generates warning alerts. |
| `criticalSensitivity`       | *string*   | the sensitivity (`insensitive`, `normal`, or `sensitive`) that generates critical alerts. |
| `maxCheckAttempts`           | *number*   | [optional] number of consecutive Warning/Critical instances before an alert is made. Default setting is 3 (1-10) |
| `trainingPeriodFrom`     | *number* | [optional] Specified training period (Uses metric data starting from the specified time) |
| `notificationInterval` | *number* | [optional] the time interval (in minutes) for re-sending notifications. If this field is omitted, notifications will not be re-sent. |
| `isMute`        | *boolean*       | [optional] whether monitoring is muted or not |

##### Example Input

```json
{
  "type": "anomalyDetection",
  "name": "anomaly detection",
  "memo": "my anomaly detection for roles",
  "scopes": [
    "myService: myRole"
  ],
  "warningSensitivity": "insensitive",
  "maxCheckAttempts": 3
}
```

#### Response (Monitoring with Anomaly Detection for Roles)

##### Success

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "anomalyDetection",
  "name": "anomaly detection",
  "memo": "my anomaly detection for roles",
  "scopes": [
    "myService: myRole"
  ],
  "warningSensitivity": "insensitive",
  "maxCheckAttempts": 3
}
```

`id` will be given and returned

##### Error

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>name</code> is empty</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the<code>memo</code>exceeds 250 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the specified service name or role details name is not registered in <code>scope</code> or <code>excludeScopes</code></td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the specified <code>warningSensitivity</code> or <code>criticalSensitivity</code> is not <code>insensitive</code> / <code>normal</code> / <code>sensitive</code></td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the notification re-sending time interval is not set at 10 minutes or more</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when a future vaule is specified for <code>trainingPeriodFrom</code></td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">Getting Monitor Configurations</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "monitor": {
    "id"  : "2cSZzK3XfmB",
    "type": "host",
    "name": "disk.aa-00.writes.delta",
    "memo": "This monitor is for Hatena Blog.",
    "duration": 3,
    "metric": "disk.aa-00.writes.delta",
    "operator": ">",
    "warning": 20000.0,
    "critical": 400000.0,
    "maxCheckAttempts": 3,
    "scopes": [
      "Hatena-Blog"
    ],
    "excludeScopes": [
      "Hatena-Bookmark: db-master"
    ]
  }
}
```

- each field is the same as when the [monitor was created](#create)

----------------------------------------------

<h2 id="update">Updating Monitor Configurations</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
</p>

As for requests and responses, just as when [creating monitors](#create), every field must be specified. If there are any insufficient items that are required, an error will be generated.
When `scopes` and `excludeScopes` are updated, the JSON which was designated will be completely overwritten. For example, by omitting an item in `scopes` when it has already been saved, `scopes` will be deleted. 

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

The updated monitoring configurations are returned. The same format as [Registering Monitor Configurations](#create).

#### Error

same errors as when [creating](#create).

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
      <td>when trying to change the type</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>name</code> is empty</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code>memo</code> exceeds 250 characters</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the monitor configuration doesn’t have a saved <code><em>&lt;monitorId&gt;</em></code> which was assigned to the query parameter</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">Deleting Monitor Configurations</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

The status of the monitor configuration just before it is deleted will be returned. The format will be the same as when [it was created](#create).

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
      <td>when the monitor configuration doesn’t have a saved <code><em>&lt;monitorId&gt;</em></code> which was assigned to the query parameter</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>
