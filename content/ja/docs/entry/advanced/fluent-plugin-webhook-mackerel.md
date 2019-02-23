---
Title: fluent-plugin-webhook-mackerelを利用する
Date: 2015-09-24T13:03:01+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/fluent-plugin-webhook-mackerel
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415122456637
---

fluent-plugin-webhook-mackerelを利用することでMackerelからのイベントをfluentdで受けとることができます。アラートの状況を記録したり、アラートをトリガーとして何らかのアクションをおこないたい場合に便利です。

## インストール

```
% gem install fluent-plugin-webhook-mackerel
```

## fluentd設定ファイルへの反映

以下はfluentdの設定ファイルの一例です。

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

データのtagは `#{tag}.#{event}` の形式で入力されます。上記の設定の場合、アラートイベントが発生した際には、 `mackerel.alert` タグでデータが入力されます。

## Webhookの設定

連携のためにMackerel側でWebhookの設定をおこないます。Webhookの設定よりfluentd連携のためのURLを以下のように指定してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150924/20150924141326.png)

連携テストをおこなうと、sampleイベントが発行されてWebhookに通知されます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150924/20150924170450.png)

fluentdのログに以下のようなログが出ていれば連携成功です。

```
2015-09-24 17:02:37 +0900 [info]: tag: mackerel.sample, payload:{"message"=>"Sample Notification from Mackerel", :event=>"sample"}
2015-09-24 17:02:37 +0900 mackerel.sample: {"message":"Sample Notification from Mackerel","event":"sample"}
2015-09-24 17:02:37 +0900 fluent.info: {"message":"tag: mackerel.sample, payload:{\"message\"=>\"Sample Notification from Mackerel\", :event=>\"sample\"}"}
```

fluentdサーバーのポートをインターネット上に露出させるのが現実的ではない場合は、前段にNginxなどのプロキシを配置すると良いでしょう。

Webhookについての詳細は、[Webhookにアラートを通知する](https://mackerel.io/ja/docs/entry/howto/alerts/webhook)、をごらんください。
