---
Title: Typetalk notifications
Date: 2015-02-16T16:19:08+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/typetalk
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083894396
---

[Typetalk](http://www.typetalk.com/) is a fun and easy to use messaging tool focused on team-based communication.

By setting Typetalk as a notification channel, you can have alert information from hosts being monitored by Mackerel broadcasted to any chat room(s) you designate. You can also receive detailed information regarding important alerts in real-time. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170919/20170919114420.png)

##Connecting to Typetalk

To configure connection to Typetalk, [add Typetalk](https://mackerel.io/my/channels?new=typetalk) as a new channel in the Channels page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150507/20150507163750.png)


For sending notifications to Typetalk, use Typetalk’s Bot API function.

To successfully register Typetalk as a notification channel, it will be necessary to obtain Typetalk API’s URL token. The following will explain how to get the token.  

####Obtaining the token

Log into Typetalk and click the Edit button in the topic in which you would like to receive notifications.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210105856.png" alt="f:id:mackerelio:20141210105856p:plain" title="f:id:mackerelio:20141210105856p:plain" class="hatena-fotolife" itemprop="image"></span></p>

The Edit button is located in the upper right corner of this screen.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141204/20141204180748.png" alt="f:id:mackerelio:20141204180748p:plain" title="f:id:mackerelio:20141204180748p:plain" class="hatena-fotolife" itemprop="image"></span></p>

The Edit button looks like this.

Next, in the Edit Topic screen, click on the “Bots” tab, click “Add bot”  and register the Mackerel notification bot.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110125.png" alt="f:id:mackerelio:20141210110125p:plain" title="f:id:mackerelio:20141210110125p:plain" class="hatena-fotolife" itemprop="image"></span></p>

Next a screen will be displayed where you can enter the new bot’s information. Enter any ID and Full Name, make sure “topic.post” under API Scope is checked, and then click “Create”.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110323.png" alt="f:id:mackerelio:20141210110323p:plain" title="f:id:mackerelio:20141210110323p:plain" class="hatena-fotolife" itemprop="image"></span></p>

The token should now be displayed at the bottom portion of the screen. Copy the “Get or post messages URL” and paste it back in Mackerel to complete registration.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110442.png" alt="f:id:mackerelio:20141210110442p:plain" title="f:id:mackerelio:20141210110442p:plain" class="hatena-fotolife" itemprop="image"></span></p>

##Optional features: about Prefix

When using Typetalk to receive notifications, it is possible to set custom prefixes which respond to the status of alerts. (If this has not been configured, no prefix will appear with alerts.)

Configure prefixes for each type of status such as happy and sad emojis, or anything that you choose.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110603.png" alt="f:id:mackerelio:20141210110603p:plain" title="f:id:mackerelio:20141210110603p:plain" class="hatena-fotolife" itemprop="image"></span></p>

For example if you have configured your Prefix settings as shown here…

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110619.png" alt="f:id:mackerelio:20141210110619p:plain" title="f:id:mackerelio:20141210110619p:plain" class="hatena-fotolife" itemprop="image"></span></p>

Your notifications will look like this!

-

If you have any questions please contact our support team at support@mackerel.io
