---
Title: External URL Monitoring
Date: 2015-07-01T17:45:58+09:00
URL: https://mackerel.io/docs/entry/external-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450099749045
---

This feature performs monitoring in http or https format.

## Configuring External URL monitors

To create a new External URL monitor click on the New Monitor button in the upper right hand corner of the Monitors page. Under the External URL tab, the following items will be displayed. Complete the form as explained below and click Create.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109170355.png)

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
|An alert will be generated if the response time is over the threshold|This External URL monitoring rule can be applied to any service to visualize its response time in the form of a service metrics graph. You can also monitor its response time by specifying a threshold value. <br>`Warning / Critical Threshold`: Even if a value greater than 15 seconds (15,000 ms) is specified, a timeout will occur when there is no response after 15 seconds. <br> `Duration`: This monitors the average value of the response times generated within the past 1 to 10 minutes as specified.|
|Max number of attempts before an alert is triggered|An alert is triggered when an abnormal event equivalent to a Warning/Critical alert occurs a specified number of times consecutively.|
|Notification interval|If there has been no change to the alert’s condition, even if the designated time is surpassed, the notification will be sent again.|
|HTTP Request Header|The HTTP Request Header can be specified. If User-Agent is not specified, `User-Agent: mackerel-http-checker/x.y.z` will be sent (`x.y.z` means version number). The default setting for the `Cache-Control` header is `no-cache`.|
|Request body|Make http request with specified request body.|
|Response Body Check|The response body will be checked to make sure a designated string is included. If one is not included, a notification will be sent.|
|Certification expiration date monitoring|The SSL certificate’s expiration date will be monitored. An alert will be sent when the number of remaining days before the expiration date falls below the threshold.|
|Do not perform certificate verification when requests are made|You can monitor without verifying the certificate when monitoring a server with a self-signed certificate.|
|Redirect according to response header|Assess the redirected response as a result.|

## About External URL monitor specs

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
     * In cases where cookies are received from the target URL that is being monitored (set-cookie), the request sent to the redirection destination page will include the cookie information received.
* If you wish to monitor URLs that use Basic Authentication
  * Specify the authorization header in the HTTP request header
    * `Name: Authorization`
    * `Value: Basic xxxxx`
      * Enter the base64-encoded string for the "user:password" (a combination of the credentials) in "xxxxx"
  * Include the credentials in the target URL to be monitored in the form "https://user:password@example.com/"

## Monitoring source IP addresses of External URL monitors
This is the same as the source IP address range for Mackerel notification requests. For more information check our [FAQ page](https://support.mackerel.io/hc/en-us/articles/360039701332).

## Subscription plans
This feature can only be used with a paid subscription or in the free trial.

Up to 20 External URL monitors can be made without additional charges being incurred.
If more than 20 are created, the excess will be charged using the conversion 20 External URL monitors equals the value of one standard host.

## Sample Alert
When a new monitor has been added it will be displayed in the list in the Monitors page.
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109120423.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109120423.png]

If an alert arises it will be displayed as shown below, at which point the details of the alert can be viewed in the alert details screen.
The 503 displayed at the top is the HTTP status code.
If the alert has been resolved, the HTTP status code at the time it was resolved will be displayed. 

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png]

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png]
