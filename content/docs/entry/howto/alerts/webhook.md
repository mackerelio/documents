---
Title: Webhook notifications
Date: 2015-09-25T17:15:33+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/webhook
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415122579725
---

Using Webhook you can receive the JSON from your Mackerel alerts with POST. This will be handy for those users who want to automate processes that occur after notifications are received.

## Adding Webhook notifications

Notification settings can be added and configured [Webhook form](https://mackerel.io/my/channels?new=webhook) in the Channels Page. Here you will need to designate the URL that will receive POSTs from Mackerel’s servers.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150924/20150924174417.png)

## JSON format

The JSON that will be sent contains the following. (Items may be added at any time)

```json
{
  "orgName": "Macker...",
  "event": "alert",
  "host": {
    "id": "22D4...",
    "name": "app01",
    "url": "https://mackerel.io/orgs/.../hosts/...",
    "type": "unknown",
    "status": "working",
    "memo": "",
    "isRetired": false,
    "roles": [
      {
        "fullname": "Service: Role",
        "serviceName": "Service",
        "serviceUrl": "https://mackerel.io/orgs/.../services/...",
        "roleName": "Role",
        "roleUrl": "https://mackerel.io/orgs/.../services/..."
      }
    ]
  },
  "alert": {
    "openedAt": 1473129912,
    "closedAt": 1473130092,
    "createdAt": 1473129912693, 
    "criticalThreshold": 1.9588528112516932, 
    "duration": 5, 
    "isOpen": true, 
    "metricLabel": "MetricName", 
    "metricValue": 2.255356387321597, 
    "monitorName": "MonitorName", 
    "monitorOperator": ">", 
    "status": "critical", 
    "trigger": "monitor", 
    "id": "2bj...",
    "url": "https://mackerel.io/orgs/.../alerts/2bj...",
    "warningThreshold": 1.4665636369580741
  }
}
```

## JSON items

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|orgName|string|name of the organization where the alert occurred|
|event|string|fixed as `alert`|
|host|object|host information (output for alerts of host metrics only)|
|service|object|service information (output for alerts of service metrics only)|
|alert|object|alert information|

### JSON items (host information)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|id|string|host ID|
|name|string|host name|
|url|string|URL of the host details|
|status|string|host status (`working`, `standby`, `poweroff`, `maintenance`)|
|memo|string|memo registered to the host|
|isRetired|boolean|whether or not retired|
|roles|array[object]|role information (if the role is configured to the host)|

### JSON items (role information)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|fullname|string|service name: role name|
|serviceName|string|service name
|serviceUrl|string|URL of the service details|
|roleName|string|role name|
|roleUrl|string|URL of the role in service details|

### JSON items (service information)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|id|string|service ID|
|memo|string|memo registered to services|
|name|string|service name|
|orgId|string|ID of the organization where the service is registered|
|roles|array[object]|role information (if the role is registered in services)|

### JSON items (alert information)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|id|string|alert ID|
|status|string|alert status (`ok`, `warning`, `critical`, `unknown`)|
|isOpen|boolean|whether or not the alert is open|
|trigger|string|what triggered the notification (`monitoring`, `manual`, `monitorDelete`, `hostRetire`)|
|url|string|URL of the alert details|
|openedAt|number|time the alert occurred (epoch seconds)|
|closedAt|number|time the alert resolved (epoch seconds)|
|createdAt|number|time the alert occurred (epoch milliseconds). A deprecated item and not recommended. Please use openedAt|
|monitorName|string|name of the monitoring item that detected the alert|
|metricLabel|string|title of the monitoring target’s metrics etc.|
|metricValue|number|metric value at the time of detection|
|criticalThreshold|number|CRITICAL threshold|
|warningThreshold|number|WARNING threshold|
|monitorOperator|string|`>` or `<`|
|duration|number|monitoring interval|

※Alert information items fluctuate depending on the type of monitoring target (host metrics, service metrics, external monitoring, expression monitoring) and each configuration item. For example, if only the URL is configured with external monitoring, information for metrics, thresholds, etc. will not be included. The type of monitoring target and the output items correspond with possible input items from the monitoring configurations screen. 

## Making use of Webhook

- [Getting notifications in chat tools with Hubot](https://mackerel.io/docs/entry/advanced/hubot)
- [Using fluent-plugin-webhook-mackerel](https://mackerel.io/docs/entry/advanced/fluent-plugin-webhook-mackerel)

## Security when using Webhook

For security purposes, we recommend taking the following measures when using Webhook.

- Use SSL
  - Does not support unique certificates or partial certificate authority
- Set up basic authentication and include the authentication information in the URL
  - Format like this `https://user:p4ssw0rd@example.com`
- Enforce IP restriction
  - For infomation of the IP address range of request origins of notifications from Mackerel, check our [FAQ page](https://support.mackerel.io/hc/en-us/articles/360039701332).
