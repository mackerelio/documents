---
Title: トレース - OpenTelemetryとは
Date: 2025-03-13T15:59:07+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333959173
---

このページではOpenTelemetryの概要について記載しています。

[:contents]

## OpenTelemetryについて

OpenTelemetryはメトリクスやログ、トレースの情報を収集・送信するツール郡の総称です。

その中にはSDKやプロトコルも含まれています。

例えば、Mackerelトレーシング機能を使うためには以下を使用します。

* **SDK** (各言語、フレームワーク用にデータを作成する)
* **Collector** (データを外部に送信する。例えばMackerelへの送信)

## SDKについて

OpenTelemetryはアプリケーション内部の情報を収集することができます。

そのため、各言語・フレームワークに合わせたSDKが作成されています。

SDKを使用することで、下図のようにリクエストにどのような操作があったかを把握することができるようになります。

<figure class="figure-image figure-image-fotolife" title="https://opentelemetry.io/ja/docs/concepts/observability-primer/ より">[f:id:mackerelio:20250305172837p:plain]<figcaption>https://opentelemetry.io/ja/docs/concepts/observability-primer/ より</figcaption></figure>

メジャーな言語に向けては公式のものが用意されているほか、有志が作成しているものもあります。 また、フレームワークやCIツールなど個別のニーズにも対応したものもあります。

例えば、以下のフレームワークに対応しています

* [Ruby On Rails](https://rubygems.org/gems/opentelemetry-instrumentation-rails) (ruby)
* [Django](https://pypi.org/project/opentelemetry-instrumentation-django/) (Python)
* [Gin](https://pkg.go.dev/go.opentelemetry.io/contrib/instrumentation/github.com/gin-gonic/gin/otelgin) (Go)
* [Express](https://www.npmjs.com/package/@opentelemetry/instrumentation-express) (Node.js)

OpenTelemetryに対応しているツールはとても多いため、フレームワークにとどまらず、次のような操作も記録することができます。

* 発行されたSQL
* AWS との通信
* キューで処理されたジョブ

下のページから対応しているツールを検索することができます。その数に驚くはずです。

[https://opentelemetry.io/ecosystem/registry/?s=&component=instrumentation&language=all:embed:cite]

### SDKでの計装方法

SDKの計装方法は以下のページを参照してください。

[インストール・実装](https://mackerel.io/ja/docs/entry/tracing/installations)


## Collectorについて

Collector (OpenTelemetry Collector) はSDKから送られてきたデータをまとめ、外部に送信するツールです。エージェントやゲートウェイとして利用できます。

Collectorのデータの送信先をMackerelトレーシング機能のエンドポイントに設定することで、データをMackerelに送信することができます。

Collectorに関する細かい動作は、以下のページが参考になります。

* [公式のドキュメント](https://opentelemetry.io/ja/docs/collector/)
* [GitHub](https://github.com/open-telemetry/opentelemetry-collector)

### Collectorのインストール方法

Collectorのインストール方法については以下のページを参考にしてください

[OpenTelemetry Collector を導入する](https://mackerel.io/ja/docs/entry/tracing/installations/opentelemetry-collector)

<h3 id="using-collector-or-not">Collectorを使うかどうかの判断について</h3>

Mackerelトレーシング機能を使用する場合、Collectorを使わずに、SDKから直接Mackerelにデータを送信することができます。

どちらが適しているかは負荷状況や言語によりますが、一般的に以下のトレードオフがあります。

* Collectorを経由する：「SDKからCollectorへの非同期通信」&「CollectorからMackerelへの非同期通信」と送信が二重になるので、安定する
* SDKから直接送信する：SDKを使うだけで動くので実装が簡単

Collectorを使用する場合は、以下のページも参照してください。

[OpenTelemetry Collector を導入する](https://mackerel.io/ja/docs/entry/tracing/installations/opentelemetry-collector)
