---
Title: Using fluent-plugin-webhook-mackerel
Date: 2015-09-25T17:19:44+09:00
URL: https://mackerel.io/docs/entry/advanced/fluent-plugin-webhook-mackerel
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415122580019
---

With fluent-plugin-webhook-mackerel it’s possible to receive events that occur in Mackerel in fluentd. This will be useful for doing things like keeping records of alert conditions, or having  alerts act as triggers for custom actions to be carried out.

## Installation

```
% gem install fluent-plugin-webhook-mackerel
```

## Applying changes to the fluentd settings file 

This is an example of a fluentd settings file.

```
<source>
  type webhook_mackerel
  tag mackerel

  # optional (values are default)
  bind 0.0.0.0
  port 3838
  mount /
</source>

<match mackerel.alert>
  ...
</match>

<match mackerel.**>
  ...
</match>

```

The data tag will be inputted in the format `#{tag}.#{event}`. When an alert event occurs with the above settings in place, data will be inputted with the tag `mackerel.alert`. 

## Webhook settings

We’ll need to configure the Webhook settings in Mackerel to integrate. Designate the URL to integrate fluentd in the Webhook settings as shown below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150924/20150924141326.png)

When testing the integration a sample event will be generated and Webhook will be notified.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150924/20150924170450.png)

If your fluentd log looks like the log below then the integration was made successfully.

```
2015-09-24 17:02:37 +0900 [info]: tag: mackerel.sample, payload:{"message"=>"Sample Notification from Mackerel", :event=>"sample"}
2015-09-24 17:02:37 +0900 mackerel.sample: {"message":"Sample Notification from Mackerel","event":"sample"}
2015-09-24 17:02:37 +0900 fluent.info: {"message":"tag: mackerel.sample, payload:{\"message\"=>\"Sample Notification from Mackerel\", :event=>\"sample\"}"}
```

If putting your fluentd server’s port out on the internet isn’t pragmatic for your particular circumstances, it might be a good idea to set up a proxy like Nginx or another in the previous step.

For more details regarding Webhook please refer to [Webhook notifications](https://mackerel.io/docs/entry/howto/alerts/webhook).
