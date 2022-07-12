---
Title: 'Send alerts to LINE '
Date: 2016-10-03T17:17:54+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/line
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687187579021
---

[LINE](http://line.me/) is a communications app that lets you enjoy voice/video calls and chatting for free.

Integrating with [LINE Notify](https://notify-bot.line.me/), alert notifications can be sent directly to a LINE chat from Mackerel. By configuring notifications from LINE Notify, alert notifications can be received in talks addressed to individual users or group talks.  

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161003/20161003170421.png)

## Add alert notifications for LINE 

To set up notifications, add a new channel from the [Channel page’s LINE form](https://mackerel.io/my/channels/-/create#line).

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161003/20161003165358.png)

By clicking "Authenticate with LINE and create a new channel" from the channel form, you will be sent to LINE Notify’s authentication screen. If you are not logged-in to LINE Notify, the login screen will be displayed. Please log-in here.  

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161003/20161003165357.png)

From the authentication screen, select a chat room to which notifications will be sent after confirming the precautions associated with the use of LINE Notify. By clicking “Agree and connect”, you will be sent to Mackerel’s channel list. At this point, a channel to send notifications from Mackerel to LINE will be created. The channel name will automatically be configured as the chat room name combined with a random string so change the name as necessary. 

### About the notification chat room

The notification channel’s chat room name can not be confirmed or changed once the channel has been created. If you want to confirm to which chat room the notifications will be sent, send a test notification with the notification channel you just created. Additionally, if you want to change the notification channel’s chat room, you will need to delete the channel and remake a new one. 
