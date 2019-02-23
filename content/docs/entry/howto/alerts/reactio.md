---
Title: Integration with Reactio
Date: 2015-10-28T18:10:13+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/reactio
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415125893091
---

***Notice:*** As of now Reactio’s services are only available in Japanese. There is a chance this may change in the future.

[Reactio](https://reactio.jp) is an alert management service that offers features for collecting alert information and sharing information through flexible notifications and customizable rules, in addition to group chat features. Possible notification methods include voice, SMS, email, and more.

When integrating with Reactio, you can choose one of the following three action sets to be executed when an alert is raised.

1. Only create an incident
2. Create an incident and send a notification
3. Create an incident and send a notification and a phone call


When an alert is generated, the alert log linked to the incident that is created will be posted as a chat message.
In this way, alerts generated in Mackerel can be collected in Reactio without sacrificing any of Reactio’s notification functionality. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160408/20160408181757.png)

In order to complete integration with Reactio you must enter your Organization ID and API key in the Reactio form in the Channels page and click Create. The Organization ID is issued when registering to Reactio. The API key can be acquired with the following steps.

 -Select Project Settings from the menu in the upper right hand corner of the Reactio main screen

 -Select Project from the Project Settings screen

 -The API key will be described as a character string in the Project Details screen
