---
Title: HipChat notifications
Date: 2015-02-13T12:31:21+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/hipchat
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083520621
---

HipChat is a private chat service used primarily within companies for real-time, interdepartmental or team-based collaboration with features including file sharing, screen sharing, multiple chat rooms and video chat across different types of devices. 

By registering HipChat as a notification channel with Mackerel, users can have alert information from hosts being monitored by Mackerel sent directly to the appropriate chatroom. When important alerts arise, you can receive the information you need in real-time. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150424/20150424105422.png)

##Adding HipChat to your channels

HipChat can be added as a new channel and configured from the HipChat form in the Channels page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150508/20150508104153.png?1431049701)

For notifications sent to HipChat, one of HipChat’s [Rooms API](https://www.hipchat.com/docs/apiv2/method/send_room_notification) will be used. 
To register the channel, in addition to the channel name, please enter the Room API ID and the Room Notification Token acquired from HipChat. Using the following instructions you can obtain the Room API ID and the Room Notification Token.

###Acquiring the Room API ID
- Click on the "Rooms" tab the [HipChat](https://my.hipchat.com) page.
- Select the chat room to which notifications will be sent.
- The item listed in the middle of the screen is the Room API ID.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141029/20141029160124.png" alt="f:id:mackerelio:20141029160124p:plain" title="f:id:mackerelio:20141029160124p:plain" class="hatena-fotolife" itemprop="image"></span></p>

###Issuing and Acquiring the Room Notification Token
- Click on the "Rooms" tab the [HipChat](https://my.hipchat.com) page.
- After you have selected the chat room to which notifications will be sent, click on "Token" in the left side bar.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141029/20141029160136.png" alt="f:id:mackerelio:20141029160136p:plain" title="f:id:mackerelio:20141029160136p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- Enter a label and then issue the new Token.
- (Tip)The label name you enter will appear as the as the notification name, so entering a name like "Mackerel" will make it easier to understand.
- The Token which will then be displayed in the center of the screen is the Room Notification Token.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141029/20141029160148.png" alt="f:id:mackerelio:20141029160148p:plain" title="f:id:mackerelio:20141029160148p:plain" class="hatena-fotolife" itemprop="image"></span></p>

In addition, when an alert is sent from Mackerel to HipChat, it's possible to have HipChat produce a desktop notification which can be configured according to the status of the alert. Please configure your settings to meet your individual needs. 

-

If you have any questions please contact our support team at support@mackerel.io
