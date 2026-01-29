---
Title: External URL Monitoring
Date: 2015-07-01T17:45:58+09:00
URL: https://mackerel.io/docs/entry/external-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450099749045
---

This feature performs monitoring in http or https format.

## Configuring External URL monitors

To create a new External URL monitor click on the New Monitor button in the upper right hand corner of the Monitors page. Under the External URL tab, the following items will be displayed. Complete the form as explained below and click Create.

#### Scope

|Configuration item|Description|
|-----|-----|
|HTTP method|You may select from GET / POST / PUT / DELETE|
|URL|Select either http or https and enter the rest of the URL.|

#### Basic Settings

|Configuration item|Description|
|-----|-----|
|Monitor Name|Enter the name of this monitoring rule.|
|Monitor Memo|You can search for the notes you have entered on the Alerts overview page.|

#### Options

|Configuration item|Description|
|-----|-----|
|An alert will be generated if the response time is over the threshold|By linking this External URL monitoring to any service, you can set an arbitrary threshold for the response time. See also [About response time service metrics](#responsetime).<br>**Note**: When this option is enabled, the response time will be posted as a service metric for the linked service, and a fee for 1 metric will be charged.|
|Warning / Critical Threshold|An alert is generated when the response time exceeds the specified number of seconds. Even if a value greater than 15,000 ms (15 seconds) is specified, a timeout will occur when there is no response after 15,000 ms.<br>**Note**: Can be configured when linked to a service.|
|Duration|This monitors the average value of the response times generated within the past 1 to 10 minutes as specified.<br>**Note**: Can be configured when linked to a service.|
|Max number of attempts before an alert is triggered|An alert is triggered when an abnormal event equivalent to a Warning/Critical alert occurs a specified number of times consecutively.|
|Notification interval|If there has been no change to the alert's condition, even if the designated time is surpassed, the notification will be sent again.|
|HTTP Request Header|The HTTP Request Header can be specified. If User-Agent is not specified, `User-Agent: mackerel-http-checker/x.y.z` will be sent (`x.y.z` means version number). The default setting for the `Cache-Control` header is `no-cache`.|
|Request body|Make http request with specified request body.|
|Status Code Check|An alert is generated if the status code is not the specified code.|
|Response Body Check|The response body will be checked to make sure a designated string is included. If one is not included, a notification will be sent.|
|Certification expiration date monitoring|The SSL certificate's expiration date will be monitored. An alert will be sent when the number of remaining days before the expiration date falls below the threshold.|
|Do not perform certificate verification when requests are made|You can monitor without verifying the certificate when monitoring a server with a self-signed certificate.|
|Redirect according to response header|Assess the redirected response as a result.|

## About External URL monitor specs

* Check intervals are fixed at 1 minute
* An error is recognized and an alert notification issued under the following conditions
    * If the status code is in the 4xx or 5xx range, or if it is a code other than the one specified in the status code check (optional setting)
    * a 15-second timeout 
    * an invalid SSL certificate  
    * When response time exceeds the threshold (optional setting)
    * When a designated string isn’t included in the response body (optional setting)
    * When the number of remaining days before the SSL certificate expiration date falls below the threshold (optional setting)
* 2xx responses are recognized as normal and not as an error
* Behavior regarding the redirect follow can be changed in settings
   * If not followed, 3xx responses will be recognized as normal and not as an error
   * If followed, the redirect will occur according to the response header
     * Three or more redirects will result in an error and an alert notification will be issued
     * In cases where cookies are received from the target URL that is being monitored (set-cookie), the request sent to the redirection destination page will include the cookie information received.
* If you wish to monitor URLs that use Basic Authentication
  * Specify the authorization header in the HTTP request header
    * `Name: Authorization`
    * `Value: Basic xxxxx`
      * Enter the base64-encoded string for the "user:password" (a combination of the credentials) in "xxxxx"
  * Include the credentials in the target URL to be monitored in the form "https://user:password@example.com/"

<h2 id="responsetime">About response time service metrics</h2>

When linked to a service with the "An alert will be generated if the response time is over the threshold" option enabled, the response time will be posted as a service metric for that service.

### How to check service metrics

1. Select the linked service from the [Service list](https://mackerel.io/my/services)
2. Select the "Service Metrics" tab

The metrics are posted to a graph called "HTTP Response Time". The metric names displayed in the graph are the names of the External URL monitor rules. If some are displayed as alphanumeric strings instead of names, these are metrics from External URL monitors that have already been deleted. If necessary, delete them from the graph definition as described in [About service metric names](#about-service-metric-names) below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20260123/20260123173607.png)

### About service metric names

Response time service metrics can also be used in expressions. When using them in expressions, the service metric name follows this format:

`__externalhttp.responcetime.<monitoring rule ID>`

- The monitoring rule ID is the string at the end of the URL on the External URL monitor edit screen.

You can also check the metric name by clicking the gear icon (graph definition) at the top right of the "HTTP Response Time" graph and hovering over the metric name in the displayed modal.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20260123/20260123173741.png)

## Monitoring source IP addresses of External URL monitors
This is the same as the source IP address range for Mackerel notification requests. For more information check our [FAQ page](https://support.mackerel.io/hc/en-us/articles/360039701332).

## Subscription plans
This feature can only be used with a paid subscription or in the free trial.

## Usage Fees

Please refer to [How usage fees are calculated](https://support.mackerel.io/hc/en-us/articles/31304727432729).

## Sample Alert
When a new monitor has been added it will be displayed in the list in the Monitors page.
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109120423.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109120423.png]

If an alert arises it will be displayed as shown below, at which point the details of the alert can be viewed in the alert details screen.
The 503 displayed at the top is the HTTP status code.
If the alert has been resolved, the HTTP status code at the time it was resolved will be displayed. 

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png]

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png]
