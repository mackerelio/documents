---
Title: トレース - Node.jsにOpenTelemetryを導入する
Date: 2025-03-13T16:38:05+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/nodejs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960594
---

MackerelはOpenTelemetryの仕組み (計装)を利用してデータを取得しています。

このページではNode.jsのデータをMackerelに送信する方法を解説します。

[:contents]

## Node.js向けOpenTelemetry

OpenTelemetry には Node.js用のSDKが用意されています。

このSDKに加えて、ExpressやAWS用のSDKを使用すると、色々な範囲を計装することができます。

[https://opentelemetry.io/ja/docs/languages/js/getting-started/nodejs/:embed:cite]

### Collectorを使用するべきか

データをMackerelに送信する際に、SDKから直接送信するだけではなく、Collectorを使うこともできます。

Collectorを使うかどうかを決める際は以下のページを参考にしてください。

[Collectorを使うかどうかの判断について](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## 導入方法

Node.jsには複数のWebフレームワークが存在しますが、このページでは `Express` への導入方法を説明します。 他のフレームワークを使っている場合もほぼ同じ方法で計装することができます。

以下のステップでMackerelトレーシング機能を導入できます。

1. パッケージ追加
2. 初期設定
3. 独自の計装の追加 (任意)

### 1. パッケージ追加

以下のパッケージを使用します。

```bash
npm install @opentelemetry/api \
  @opentelemetry/auto-instrumentations-node \
  @opentelemetry/exporter-trace-otlp-proto \
  @opentelemetry/resources \
  @opentelemetry/sdk-node \
  @opentelemetry/sdk-trace-node \
  @opentelemetry/semantic-conventions
```

上のコマンドで追加した `auto-instrumentations-node` は多くのパッケージを内包しているため、Expressやhttpのように人気のライブラリは自動で計装されます。

含まれているパッケージは[GitHubのページ](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/packages/auto-instrumentations-node#supported-instrumentations)を参照してください。

また、足りない場合はOpenTelemetryのページからパッケージを探すことができます。

[https://opentelemetry.io/ecosystem/registry/?language=js&component=instrumentation:embed:cite]

### 2. 初期設定

OpenTelemetryのデータをMackerelに送信するためには、以下の項目を設定する必要があります。

* Exporter
* resource

例えば、以下のようにすると、SDKから直接Mackerelに送信することができます。

```javascript
const { resourceFromAttributes, processDetector, hostDetector } = require('@opentelemetry/resources');
const { OTLPTraceExporter } = require("@opentelemetry/exporter-trace-otlp-proto");
const { ConsoleSpanExporter } = require('@opentelemetry/sdk-trace-node');
const { NodeSDK } = require('@opentelemetry/sdk-node');
const { getNodeAutoInstrumentations } = require('@opentelemetry/auto-instrumentations-node');
const {
  ATTR_SERVICE_NAME,
  ATTR_SERVICE_VERSION,
} = require("@opentelemetry/semantic-conventions");

// ATTR_DEPLOYMENT_ENVIRONMENT_NAME は個別に定義
ATTR_DEPLOYMENT_ENVIRONMENT_NAME = 'deployment.environment.name';

const exporter = new OTLPTraceExporter({
  maxQueueSize: 1000,
  url: "https://otlp-vaxila.mackerelio.com/v1/traces",
  headers: {
    "Accept": "*/*",
    "Mackerel-Api-Key": process.env.MACKEREL_API_KEY,
  }
})
// デバッグ時にはConsoleSpanExporterが便利
// const exporter = new ConsoleSpanExporter()

const sdk = new NodeSDK({
  traceExporter: exporter,
  instrumentations: [
    getNodeAutoInstrumentations({
      // fsの計装はスタートアップ時に大量のトレースを作り出すので、必要がなければ外したほうが便利です。
      '@opentelemetry/instrumentation-fs': {
        enabled: false,
      },
    }),
  ],
  resource: resourceFromAttributes({
    [ATTR_SERVICE_NAME]: "acme_service",
    [ATTR_SERVICE_VERSION]: "vX.Y.Z",
    [ATTR_DEPLOYMENT_ENVIRONMENT_NAME]: "production"
  }),
  resourceDetectors: [processDetector, hostDetector]
});

sdk.start()
```

この例では次の項目を設定しています。

* Exporter
  * url
    * `https://otlp-vaxila.mackerelio.com/v1/traces` と設定することで、データを直接Mackerelに送信するようになります。
    * Collectorを利用する場合は、Collectorのエンドポイント (`http://localhost:4318/v1/traces` など) を設定してください。
  * headers
    * `Mackerel-Api-Key` と `Accept` のヘッダーを設定することでMackerelと通信することができます。
    * `Mackerel-Api-Key` には、Mackerelで発行された書き込み権限のあるAPIキーを設定してください。APIキーの権限を変更した際は反映まで1分ほどお待ちください。
    * Collectorを利用する場合、ヘッダーは必要ないでしょう。
* resource & resourceDetectors
  * NodeSDKのresourceを設定することで、データがどこから来たかわかるようになります。
  * `ATTR_DEPLOYMENT_ENVIRONMENT_NAME` は2025年5月時点で `@opentelemetry/semantic-conventions/incubating` に含まれますが、このパッケージは不安定であるため、[Unstable SemConv](https://www.npmjs.com/package/@opentelemetry/semantic-conventions#unstable-semconv) で推奨されるように、import せず個別に定義しています。将来的には `@opentelemetry/semantic-conventions` で使用できるようになると思われます。

### 3. 独自の計装の追加 (任意)

独自のSpanを追加することで、任意の範囲を計装することができます。

計装によって、変数の値や処理時間を記録することができるようになります。

具体的には、下のように `startActiveSpan` で囲むと計装が追加できます。

```javascript
const tracer = opentelemetry.trace.getTracer(
  'my-service-tracer'
);
tracer.startActiveSpan('awesome_action', (span) => {
  // ... 既存の処理

  span.end();
});
```

計装の方法は他にも用意されています。詳細はOpenTelemetryのドキュメントを参照してください。

[https://opentelemetry.io/ja/docs/languages/js/instrumentation/:embed:cite]
