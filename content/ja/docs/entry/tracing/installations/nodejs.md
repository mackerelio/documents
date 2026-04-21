---
Title: Node.jsのアプリケーションからMackerelにトレースを送信する
Date: 2025-03-13T16:38:05+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/nodejs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960594
---

このページでは、Node.jsのアプリケーションからトレースをMackerelに送信する方法を解説します。

[:contents]

## 概要

MackerelはOpenTelemetryの仕組み（計装）を利用してトレースを取得します。OpenTelemetryに対応したトレースはさまざまな方法で取得できますが、今回はゼロコード計装と呼ばれる、アプリケーションの実装を変更せずにトレースを送信する方法を解説します。

<div class="note">
<p>📝 補足</p>
<p>Node.jsのゼロコード計装は、nodeコマンドによるアプリケーション実行時に @opentelemetry/auto-instrumentations-node パッケージにて提供されるファイルをrequireし、トレースなどのテレメトリーデータを取得できるようにすることで実現されています。</p>
<p>詳しくはOpenTelemetry公式ドキュメントの <a href="https://opentelemetry.io/ja/docs/zero-code/js/">Node.jsゼロコード・計装 | OpenTelemetry</a> をご確認ください。</p>
</div>

## 動作要件

OpenTelemetryの[動作要件](https://github.com/open-telemetry/opentelemetry-js/tree/main?tab=readme-ov-file#supported-runtimes)として、Node.jsが指定されたバージョン以上である必要があります。

- Node.js v18以上

## 導入方法

アプリケーションからMackerelへトレースを送信するために、以下をおこないます。

1. パッケージのインストール
2. インストールされたファイルをrequireする設定とともにアプリケーションを起動

### 1. パッケージのインストール

以下のコマンドで、Node.jsでのOpenTelemetry利用に必要なパッケージをインストールします。

```bash
npm install --save @opentelemetry/api @opentelemetry/auto-instrumentations-node
```

OpenTelemetryはNode.jsのさまざまなライブラリをサポートしており、ライブラリごとに個別の計装ライブラリが用意されています。計装ライブラリを導入することで、対象となるライブラリからテレメトリーが取得できます。提供されているパッケージは以下のページで検索できます。

[https://opentelemetry.io/ecosystem/registry/?language=js&component=instrumentation:embed:cite]

<div class="note">
<p>📝 補足</p>
<p>お使いのフレームワークやアプリケーション向けの計装ライブラリが存在しない場合は、独自に計装することもできます。</p>
<p>詳しくはOpenTelemetry公式ドキュメントの <a href="https://opentelemetry.io/ja/docs/languages/js/instrumentation/">計装 | OpenTelemetry</a>をご確認ください。</p>
</div>

### 2. インストールされたファイルを require する設定とともにアプリケーションを起動

ゼロコード計装を利用する場合、`@opentelemetry/auto-instrumentations-node/register` を起動時にrequireしつつNode.jsアプリケーションを実行することで、アプリケーションから自動でトレースを送信できます。

環境変数 `MACKEREL_APIKEY` にMackerelのWrite権限を持つAPIキーを設定した状態で、以下のようにそのほか取得・投稿に必要な環境変数を指定し起動することで、`my-sample-app` というサービス名でMackerelにトレースが送信されます。

```sh
env OTEL_TRACES_EXPORTER=otlp \
OTEL_METRICS_EXPORTER=none \
OTEL_LOGS_EXPORTER=none \
OTEL_SERVICE_NAME=my-sample-app \
OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=https://otlp-vaxila.mackerelio.com/v1/traces \
OTEL_EXPORTER_OTLP_TRACES_HEADERS=Mackerel-Api-Key=${MACKEREL_APIKEY},Accept='*/*' \
OTEL_EXPORTER_TOLP_TRACES_PROTOCOL=http/protobuf \
NODE_OPTIONS="--require @opentelemetry/auto-instrumentations-node/register" \
node app.js # app.js は起動するアプリケーションファイルに置き換えてください。
```

- `OTEL_TRACES_EXPORTER` を `console` にするとトレースが標準出力に出力されます。
- `OTEL_SERVICE_NAME` はトレースのサービス名（`service.name`属性の値）になります。
- `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` はトレースの送信先の指定です。
  - Mackerelに直接送信する場合は `https://otlp-vaxila.mackerelio.com/v1/traces` を指定します。
  - Collectorを利用して送信する場合は `http://<Collectorのアドレス:ポート>/v1/traces` を指定します。
- `${MACKEREL_APIKEY}` はMackerelのAPIキーの指定です。[APIキーの一覧](https://mackerel.io/my?tab=apikeys)から、Write権限のあるAPIキーをアプリケーションが動作するシステム内の環境変数に定義してください。
  - 環境変数ではなくAPIキーを直接記述しても動作します。

## トレースを確認する

送信されたトレースは以下の手順で確認できます。

1. メニューの「[APM](https://mackerel.io/my/apm)」を選択<br>
2. サービス名を選択
  [f:id:mackerelio:20251224180534j:plain]
3. 「トレース」タブを選択
  [f:id:mackerelio:20251224180530j:plain]
4. トレース一覧からトレースを選択すると詳細が確認できます
  [f:id:mackerelio:20251224180525p:plain]

以上、Node.jsで作成されたアプリケーションにゼロコード計装をおこなって、Mackerelへトレースを投稿する方法のご紹介でした。
