---
Title: Yammer Notifications
Date: 2016-03-14T12:49:23+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/yammer
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328537792367019403
---

Yammer is a private social network that promotes employee collaboration across departments, locations, and business applications. 
Linked with Mackerel’s channel features, alert notifications can be sent to Yammer groups and the alert content can easily be shared amongst group members.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114113016.png)

### Adding Notifications to Yammer 

In Alerts settings, add a new channel from the [channel page’s Yammer form](https://mackerel.io/orgs/hatena/channels/-/create#yammer). 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160314/20160314124108.png)

### How to obtain the Access Token and Group ID

The Access Token and Group ID are needed to register a channel. The following steps describe how to obtain them. 

Shown below are the steps for obtaining the Access Token where the Expected redirect (redirect_uri) is set to `https://localhost`; the `Client ID` and `Client secret` obtained in step 2 are `mackerel_client` and `mackerel_secret`; and the code obtained in step 3 is `mackerel_code`. Please change these values as well as the URLs to be accessed to meet your environment.

1) To register an application linked with Mackerel, go to[ the application registration page.](https://www.yammer.com/client_applications) To find out how to register an application, please reference [the official page](https://developer.yammer.com/docs/app-registration). 

2) Obtain the registered application’s `Client ID` and `Client secret`. Select the registered application from the application registration page. The `Client ID` and `Client secret` are listed from the screen.  

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104339.png)

3) Obtain the registered application’s `code`. With the `client_id` and the `redirect_ uri` queries configured, the Code can be found by accessing the following URL link.

`https://www.yammer.com/oauth2/authorize?client_id=mackerel_client&response_type=code&redirect_uri=https%3A%2F%2Flocalhost`

When the browser accesses an appropriate URL, it will transfer to the application authentication screen. Click the “allow” button. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104340.png)

After clicking “allow” , reference the section displayed in the browser’s URL to find the code. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104341.png)

4) Obtain the registered application’s Access Token. With the `client_id`, `client_secret`, and code queries configured, the Access Token can be obtained by accessing the following URL link. 

The “token” key value included in the returned JSON is the `Access Token`. 

### How to obtain the Group ID

The Group ID can be obtained by using the following steps. 

Access the group page where the user is planning to post Mackerel's alert notifications to. 
Obtain the feedId query value displayed in the browser’s URL.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104342.png)

The feedId value is the Group ID.

### About the number of messages submitted to the group when linked with Mackerel

At the present time (the latest modification to the help page), [the number of potential message API calls to Yammer is limited to 10 in 30 seconds](https://developer.yammer.com/docs/rest-api-rate-limits). In the event that the alert limit is exceeded, the notifications sent during the exceeded time period will not be delivered to the group. Please keep this in mind. 
