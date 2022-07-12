---
Title: Typetalk notifications
Date: 2015-02-16T16:19:08+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/typetalk
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083894396
---

[Typetalk](http://www.typetalk.com/) is a fun and easy-to-use messaging tool that focuses on team-based communication.

By configuring Typetalk as a notification channel, you can have alert information from hosts that are being monitored by Mackerel broadcasted to any designated chat room(s). You can also receive detailed information regarding important alerts in real-time. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150507/20150507163216.png)

##Connecting to Typetalk

To connect to Typetalk, [add Typetalk](https://mackerel.io/my/channels/-/create#typetalk) as a new channel in the Channels page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150507/20150507163750.png)

Typetalk’s Bot API function is used to send notifications to Typetalk.

In order to successfully register Typetalk as a notification channel, you'll need to obtain the Typetalk API URL and token. Continue reading for an explaination on how to obtain these items.  

####Obtaining the token

Log into Typetalk and go to the settings page of your selected Topic.

Next, in the Topic's settings screen, click on the “Bots” tab, then click “Add bot”  to register the Mackerel notification bot.

Fill out the necessary information. At this time, give the bot permission to post messages.

In the next screen, you can obtain the posting URL and Typetalk Token.

Register both the posting URL and the Token as shown below in the Mackerel settings screen.

```
https://typetalk.com/api/v1/topics/{The ID listed in the URL}?typetalkToken={The Token}
```
※If you are unsure about any of Typetalk's detailed settings, please contact Typetalk support

##Optional function: Prefix

With Typetalk, you can set custom prefixes to correlate with alert statuses. (If this is not configured, prefixes will not appear with alerts.)

When setting up the channel, set a prefix for each status that is easy to understand such as a happy emoji, sad emoji, or anything of your choosing.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110603.png" alt="f:id:mackerelio:20141210110603p:plain" title="f:id:mackerelio:20141210110603p:plain" class="hatena-fotolife" itemprop="image"></span></p>

For example, if you configure your Prefix settings like shown above…

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110619.png" alt="f:id:mackerelio:20141210110619p:plain" title="f:id:mackerelio:20141210110619p:plain" class="hatena-fotolife" itemprop="image"></span></p>

Your notifications will look like this!

-

If you have any questions please contact our support team at support@mackerel.io
