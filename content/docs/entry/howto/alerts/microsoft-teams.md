---
Title: Microsoft Teams notifications
Date: 2019-02-07T12:09:59+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/microsoft-teams
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/98012380860295260
---

[Microsoft Teams](https://teams.microsoft.com) is the hub for teamwork in Office 365.

Using the  Microsoft Teams notification channel, you can send alert notifications from Mackerel to the Microsoft Teams channel.

### Add alert notifications for Microsoft Teams

**As of August 2024, notifications can't be sent to private channels due to Microsoft Teams restrictions.**
**Microsoft says they're working on it and it is expected to be available in the future, so please wait until then.**

In Microsoft Teams, click "Apps" > "Workflows" > "Notifications", and then select "Post to a channel when a webhook request is received".
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821123205.png" alt="f:id:mackerelio:20240821123205:plain" title="f:id:mackerelio:20240821123205:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

Enter a Name of your choice for the workflow and verify that the Connections is valid.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821123211.png" alt="f:id:mackerelio:20240821123211:plain" title="f:id:mackerelio:20240821123211:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

Select the Team and Channel you want to send notifications to.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821123219.png" alt="f:id:mackerelio:20240821123219:plain" title="f:id:mackerelio:20240821123219:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

Click the copy button to copy the webhook URL.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821123224.png" alt="f:id:mackerelio:20240821123224:plain" title="f:id:mackerelio:20240821123224:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

Select a Microsoft Teams channel from Mackerel's notification channel settings screen and specify the URL of the webhook you copied in the previous step in the URL.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821123228.png" alt="f:id:mackerelio:20240821123228:plain" title="f:id:mackerelio:20240821123228:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

When you send a test notification, you can see the following.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240827/20240827120714.png" alt="f:id:mackerelio:20240827120714:plain" title="f:id:mackerelio:20240827120714:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

When an alert is triggered, a notification is sent as follows.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240827/20240827120338.png" alt="f:id:mackerelio:20240827120338:plain" title="f:id:mackerelio:20240827120338:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

In addition, some Markdown notation can be used for memo for monitoring rules and alerts, as shown below.
Also, you can send mentions by specifying a user identifier such as `<at>****@****.onmicrosoft.com</at>`.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821123925.png" alt="f:id:mackerelio:20240821123925:plain" title="f:id:mackerelio:20240821123925:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821124310.png" alt="f:id:mackerelio:20240821124310:plain" title="f:id:mackerelio:20240821124310:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

### [Deprecated] Add alert notifications for Microsoft Teams with Incoming Webhook

**Microsoft has announced the phasing out of Incoming Webhooks, and as of August 15, 2024, you will no longer be able to create new Incoming Webhooks.**
**If you have been using Incoming Webhooks, you can continue to use it until December 31, 2025 by completing the extension procedure provided by Microsoft by January 31, 2025.**
**For more information, see [Retirement of Office 365 connectors within Microsoft Teams](https://devblogs.microsoft.com/microsoft365dev/retirement-of-office-365-connectors-within-microsoft-teams/).**

**Also, with this method, you can't send a mention even if you specify `<at>` in the notification.**

Click on the Microsoft Teams channel connector and create an Incoming Webhook.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207162159.png" alt="f:id:mackerelio:20190207162159:plain" title="f:id:mackerelio:20190207162159:plain" class="hatena-fotolife" itemprop="image" width="400"></span></p>
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207162249.png" alt="f:id:mackerelio:20190207162249:plain" title="f:id:mackerelio:20190207162249:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
Use the [Brand Assets](https://mackerel.io/brand-assets/) PNG image to customize the image.

Specify the generated URL in the [Mackerel Settings screen](https://mackerel.io/my/channels/-/create#microsoft-teams) and create the channel.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207162452.png" alt="f:id:mackerelio:20190207162452:plain" title="f:id:mackerelio:20190207162452:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
Confirm by sending a test notification. 
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207162718.png" alt="f:id:mackerelio:20190207162718:plain" title="f:id:mackerelio:20190207162718:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
When an alert occurs, notifications will be sent in the following manner.
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207172353.png" alt="f:id:mackerelio:20190207172353:plain" title="f:id:mackerelio:20190207172353:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>


