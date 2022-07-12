---
Title: Chatwork notifications
Date: 2015-02-16T18:38:12+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/chatwork
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083918109
---

[Chatwork](http://www.chatwork.com/) is a communication tool that takes the place of emails, telephone calls, and meetings, improving efficiency and communication in the workplace and helping your business to grow.

By registering Chatwork as a notification channel with Mackerel, users can have alert information from hosts being monitored by Mackerel sent directly to the appropriate chatroom so that when important alerts arise, you can receive the information you need in real-time.

<!--![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141204/20141204112031.png)-->

###Adding Chatwork to your channels

Chatwork can be added as a new channel and configured from the [Chatwork form](https://mackerel.io/my/channels/-/create#chatwork) in the Channels page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190121/20190121191504.png)

For notifications to Chatwork, only the API that adds a new message to the chat is used from the Chatwork API.

To register the channel, other than giving the channel a name you will need to obtain the token for using the Chatwork API as well as the ROOM ID of the chat room you want to have notifications sent to. The token and ROOM ID can be obtained as shown below.

#####Acquiring the token

- After logging into [Chatwork](https://www.chatwork.com) click on Personal Settings in the upper right hand corner of the screen.
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160319/20160319191116.png)
- Select the API発行 tab and enter your password to generate the token. (It appears that this settings tab in Chatwork has not yet been translated into English at this time.)
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160319/20160319191112.png)
- Copy the token which is generated, return to Mackerel and paste it into the Chatwork channel form.
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160319/20160319191107.png)

#####Acquiring the ROOM ID

- After logging into [Chatwork](https://www.chatwork.com), go to the chat room you want to have notifications sent to.
- The string of digits at the end of the URL following `rid` is the ROOM ID.
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190123/20190123181957.png)

-

If you have any questions please contact our support team at support@mackerel.io
