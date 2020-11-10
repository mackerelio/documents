---
Title: External URL Monitoring
Date: 2015-07-01T17:45:58+09:00
URL: https://mackerel.io/docs/entry/external-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450099749045
---

This feature performs monitoring in http or https format.

## Configuring external URL monitors
To create a new external URL monitor click on the New Monitor button in the upper right hand corner of the Monitors page. Under the External Http tab, the following items will be displayed. Complete the form as explained below and click Create.

* URL: select either http or https and enter the rest of the URL in the form.
* Alert Conditions: Connect the monitor rule to a service and the response time will be submitted as a service metric. Graphs can be seen in the service metrics tab. Also, the threshold can be configured for response time. 
* Response Body Check: The response body will be checked to make sure a designated string is included. If one is not included, a notification will be sent.
* Re-sent Notification Intervals: If there has been no change to the alert’s condition, even if the designated time is surpassed, the notification will be sent again.
* HTTP Request Header: Any HTTP header can be specified for the request.
  * If User-Agent is not specified, `User-Agent: mackerel-http-checker/x.y.z` will be sent (`x.y.z` means version number).
  * The Cache-Control header is specified as `no-cache` by default.
* Certificate Expiration Date Monitor: The SSL certificate’s expiration date will be monitored. An alert will be sent when the number of remaining days before the expiration date falls below the threshold.
* Monitor Name: enter a descriptive name for the new monitor.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109170355.png)

## About external URL monitor specs

* Check intervals are fixed at 1 minute
* An error is recognized and an alert notification issued under the following conditions
    * 4xx or 5xx status codes 
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

* To monitor a URL that’s using Basic access authentication,  specify the Authorization header appropriately or include the authentication information in the URL like "https: // user: password@example.com/...".

## Monitoring source IP addresses of external URL monitors
This is the same as the source IP address range for Mackerel notification requests. For more information check our [FAQ page](https://support.mackerel.io/hc/en-us/articles/360039701332).

## Subscription plans
This feature can only be used with a paid subscription or in the free trial.

Up to 20 external URL monitors can be made without additional charges being incurred.
If more than 20 are created, the excess will be charged using the conversion 20 external URL monitors equals the value of one standard host.

## Sample Alert
When a new monitor has been added it will be displayed in the list in the Monitors page.
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109120423.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109120423.png]

If an alert arises it will be displayed as shown below, at which point the details of the alert can be viewed in the alert details screen.
The 503 displayed at the top is the HTTP status code.
If the alert has been resolved, the HTTP status code at the time it was resolved will be displayed. 

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png]

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png]
