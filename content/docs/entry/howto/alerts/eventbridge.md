---
Title: Amazon EventBridge notifications
Date: 2020-01-22T00:00:00+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/eventbridge
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613498452685
---

In Mackerel, JSON of alert contents can be received with Amazon EventBridge. This can be useful for tasks such as using AWS Lambda etc. to automate post-notification processing and more.

## Adding Amazon EventBridge notifications

### Configuration from the console

Notification settings can be added using the [Amazon EventBridge form on the Channels page](https://mackerel.io/my/channels/-/create#amazon-event-bridge). Specify your AWS account ID, the AWS region that will receive the notifications, and an event name to identify the notifications.

<img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191219/20191219142116.png" style="max-width:400px">

- For how to check your AWS account ID, please refer to [AWS Account Identifiers - AWS General Reference](https://docs.aws.amazon.com/en_us/general/latest/gr/acct-identifiers.html).
- Mackerel's event source name with Amazon EventBridge follows the format below.
  - `aws.partner/mackerel.io/{organization name}/{customer specified event name}`

### Integrated configuration from the AWS console

Once you've completed the configuration in Mackerel, you'll need to create an event bus/rule with Amazon EventBridge in order to receive notifications.

1. Open the [Partner Event Sources](https://console.aws.amazon.com/events/home#/partners) page from the AWS console.
2. Switch the console's region of operation to the AWS region to receive notifications that was set in Mackerel.
3. Select the added event source and press the 'Associate with event bus' button in the upper right.
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191219/20191219142103_original.png" style="max-width:400px">
4. Select the necessary access permission and click the 'Associate' button.
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191219/20191219142045_original.png" style="max-width:400px">
5. Open the Rules page from the leftside panel, select the event bus that you created from the drop-down list, and click 'Create rule' at the bottom.
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191219/20191219142057_original.png" style="max-width:400px">
6. In the Create rule screen, set the name, pattern, and targets. Then, in the Select event bus section, select the 'Custom or partner event bus' option and set your event bus.
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191219/20191219142110_original.png" style="max-width:400px">
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191219/20191219142051_original.png" style="max-width:400px">

## JSON format

The JSON that will be sent contains the following. (Items may be added at any time)

```javascript
{
  "orgName": "Macker...",
  "event": "alert",
  "memo": "memo....",
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
    "statusChangedAt": 1473130092,
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
  },
  "resourceInfo": {
    "service":"ec2",
    "region":null,
    "accountId":null,
    "resourceType":"instance",
    "resourceId":"i-12345..."
  }
}
```

## JSON items

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|orgName|string|name of the organization where the alert occurred|
|event|string|fixed as `alert`|
|memo|string|notes for the monitoring configuration.|
|host|object|host information (output for alerts of host metrics only)|
|service|object|service information (output for alerts of service metrics only)|
|alert|object|alert information|
|resourceInfo|object|AWS resource information (output for supported components only)|

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

### JSON Items (Metric information by query monitoring)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|labels|object|labels of the metric e.g. `{ "http.method": "GET", "http.status_code": "200" }`|
|name|string|metric name e.g. `httpcheck.status`|

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
|statusChangedAt|number|time the alert status changed (epoch seconds)|
|createdAt|number|time the alert occurred (epoch milliseconds). A deprecated item and not recommended. Please use openedAt|
|monitorName|string|name of the monitoring item that detected the alert|
|metricLabel|string|title of the monitoring target’s metrics etc.|
|metricValue|number|metric value at the time of detection|
|criticalThreshold|number|CRITICAL threshold|
|warningThreshold|number|WARNING threshold|
|monitorOperator|string|`>` or `<`|
|duration|number|monitoring interval|

※Alert information items fluctuate depending on the type of monitoring target (host metrics, service metrics, external monitoring, expression monitoring, query monitoring) and each configuration item. For example, if only the URL is configured with external monitoring, information for metrics, thresholds, etc. will not be included. The type of monitoring target and the output items correspond with possible input items from the monitoring configurations screen. 

### JSON items (AWS resource information)

#### EC2

When an alert occurs in an EC2 instance installed with mackerel-agent or AWS integration, the information that has already been acquired by Mackerel<a href="#1">*1</a> will be returned with each field corresponding to ARN format.

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|service|string|[optional] service name `ec2`|
|region|string|[optional] region name|
|accountId|string|[optional] AWS account ID|
|resourceType|string|[optional] resource type `instance`|
|resourceId|string|[optional] resource ID (EC2 instance ID) `i-12345...`|

<div id="1" style="position:relative; top:-80px;"></div>
<a href="#1">*1</a> However, you cannot retrieve this information if `cloud_platform` is set to `none` in the mackerel-agent settings.

