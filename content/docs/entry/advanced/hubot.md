---
Title: Getting notifications in chat tools with Hubot
Date: 2015-02-16T17:53:03+09:00
URL: https://mackerel.io/docs/entry/advanced/hubot
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083906607
---

Note: We will no longer maintain [hubot-mackerel-notifier][] script, and it has been **DEPRECATED**.

Using [Hubot][] with the [hubot-mackerel-notifier][] script, you can receive alert notification Webhooks from Mackerel as well as broadcast notifications like the ones shown below to multiple chat tools such as IRC and Slack. (With Slack, notifications are not only sent via Hubot, but can also be sent directly. For more information about Slack notifications please refer to [Slack notifications](https://mackerel.io/docs/entry/howto/alerts/slack).
```
17:06 hubot: [Mackerel] CRITICAL: IOwait at app01 (working) Service: app https://mackerel.io/orgs/.../alerts/...
```
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140915/20140915165010.png?1410767426)

It is also possible to have your bot display a link to a host’s details page in response to typing in that host’s name.
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140915/20140915165009.png?1410767427)

To configure the Webhook request URL pass, append `/hubot/mackerel` to the URL. For example if you are setting your bot up with `http://some-hubot.herokuapp.com/` , the Webhook URL should look like this `http://some-hubot.herokuapp.com/hubot/mackerel` . For more details about setting up the Webhook URL please refer to [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts).

For detailed information about Hubot, its source code, and installation guidance please refer to [Hubot’s master documents](https://github.com/github/hubot/tree/master/docs).

##Adding hubot-mackerel-notifier to Hubot

* First, install hubot-mackerel-notifier.
```
npm install hubot-mackerel-notifier --save
```
* Then add `hubot-mackerel-notifier` to `external-scripts.json` as follows.
```
["hubot-mackerel-notifier"]
```
* And commit it to the repository.
```
git commit -a -m 'add hubot-mackerel-notifier'
git push
```
* Next configure the environment variables, e.g. (if deploying on Heroku):
```
heroku config:add HUBOT_MACKEREL_API_KEY="..."
heroku config:add HUBOT_MACKEREL_HOST_REGEXP="\\b[a-zA-Z0-9._-]+[.]local-domain\\b"
heroku config:add HUBOT_MACKEREL_ORG_NAME="example"
heroku config:add HUBOT_MACKEREL_NOTIFIER_ROOM="#general"
```
* Finally, deploy Hubot, e.g. (if deploying in Heroku):
```
git push heroku master
```
[Hubot]: https://hubot.github.com/
[hubot-mackerel-notifier]: https://github.com/mackerelio/hubot-mackerel-notifier

Enjoy!
