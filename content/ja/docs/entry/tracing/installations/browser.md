---
Title: トレース - ブラウザにOpenTelemetryを導入する
Date: 2026-03-11T00:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/browser
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901365859462
---

MackerelはOpenTelemetryの仕組み（計装）を利用してデータを取得しています。

このページではブラウザで動作するWebアプリケーションのデータをMackerelに送信する方法を解説します。

[:contents]

## ブラウザ向けOpenTelemetry

OpenTelemetryにはブラウザ用のSDK（`@opentelemetry/sdk-trace-web`）が用意されています。

このSDKを使用して、ページの読み込み時間やユーザー操作などのパフォーマンスデータを計測できます。

[https://opentelemetry.io/ja/docs/languages/js/getting-started/browser/:embed:cite]

## 前提条件

ブラウザからMackerelにトレースを投稿するには、クライアントトークンが必要です。事前にダッシュボードからクライアントトークンを作成してください。

クライアントトークンの詳細については以下のページを参照してください。

[クライアントトークン](https://mackerel.io/ja/docs/entry/tracing/guide/client-token)

## 導入方法

以下のステップでMackerelトレーシング機能を導入できます。

1. パッケージ追加
2. 初期設定
3. サンプリングの設定

### 1. パッケージ追加

以下のパッケージを使用します。

```bash
npm install @opentelemetry/api \
  @opentelemetry/sdk-trace-web \
  @opentelemetry/sdk-trace-base \
  @opentelemetry/exporter-trace-otlp-proto \
  @opentelemetry/instrumentation-document-load \
  @opentelemetry/instrumentation-fetch \
  @opentelemetry/context-zone \
  @opentelemetry/resources \
  @opentelemetry/semantic-conventions
```

### 2. 初期設定

OpenTelemetryのデータをMackerelに送信するためには、以下の項目を設定する必要があります。

* Exporter
* resource

例えば、以下のようにすると、SDKからMackerelに送信することができます。

```javascript
import { WebTracerProvider } from '@opentelemetry/sdk-trace-web';
import { OTLPTraceExporter } from '@opentelemetry/exporter-trace-otlp-proto';
import { SimpleSpanProcessor } from '@opentelemetry/sdk-trace-base';
import { DocumentLoadInstrumentation } from '@opentelemetry/instrumentation-document-load';
import { FetchInstrumentation } from '@opentelemetry/instrumentation-fetch';
import { ZoneContextManager } from '@opentelemetry/context-zone';
import { resourceFromAttributes } from '@opentelemetry/resources';
import {
  ATTR_SERVICE_NAME,
  ATTR_SERVICE_VERSION,
} from '@opentelemetry/semantic-conventions';

// ATTR_DEPLOYMENT_ENVIRONMENT_NAME は個別に定義
const ATTR_DEPLOYMENT_ENVIRONMENT_NAME = 'deployment.environment.name';

// Client Token を使用した Exporter の設定
const exporter = new OTLPTraceExporter({
  url: 'https://otlp.mackerelio.com/v1/traces',
  headers: {
    'X-Mackerel-Client-Token': 'YOUR_CLIENT_TOKEN_HERE',
  },
});

// TracerProvider の設定
const provider = new WebTracerProvider({
  resource: resourceFromAttributes({
    [ATTR_SERVICE_NAME]: 'my-web-app',
    [ATTR_SERVICE_VERSION]: 'vX.Y.Z',
    [ATTR_DEPLOYMENT_ENVIRONMENT_NAME]: 'production',
  }),
  spanProcessors: [new SimpleSpanProcessor(exporter)],
  // 本番環境では BatchSpanProcessor の使用を検討してください
  // spanProcessors: [new BatchSpanProcessor(exporter)],
});

provider.register({
  // ZoneContextManager を使用すると非同期操作がサポートされます
  contextManager: new ZoneContextManager(),
});

registerInstrumentations({
    instrumentations: [
    // DocumentLoadInstrumentation を使用してページ読み込みのパフォーマンスを計測
    new DocumentLoadInstrumentation(),
    // FetchInstrumentation を使用して Fetch API による HTTP リクエストのパフォーマンスを計測
    new FetchInstrumentation()
],
});
```

この例では次の項目を設定しています。

* Exporter
  * url
    * `https://otlp.mackerelio.com/v1/traces` と設定することで、データをMackerelに送信するようになります。
  * headers
    * `X-Mackerel-Client-Token` ヘッダーにクライアントトークンを設定します。
    * クライアントトークンの作成方法は[クライアントトークン](https://mackerel.io/ja/docs/entry/tracing/guide/client-token)を参照してください。
* resource
  * `ATTR_SERVICE_NAME` にサービス名を設定することで、データがどこから来たかわかるようになります。
  * `ATTR_DEPLOYMENT_ENVIRONMENT_NAME` は2025年5月時点で `@opentelemetry/semantic-conventions/incubating` に含まれますが、このパッケージは不安定であるため、[Unstable SemConv](https://www.npmjs.com/package/@opentelemetry/semantic-conventions#unstable-semconv) で推奨されるように、import せず個別に定義しています。
 
以下のようにトレースが投稿されます。

[f:id:mackerelio:20260317141344p:plain]

### 3. サンプリングの設定

ブラウザからのトレースはユーザー数に比例して大量に発生します。レートリミットの超過やコスト増加を防ぐため、サンプリングの設定を推奨します。

`TraceIdRatioBasedSampler` を使用すると、トレースの一定割合のみを送信するように設定できます。前述の初期設定コードに `sampler` オプションを追加してください。

```javascript
import { TraceIdRatioBasedSampler } from '@opentelemetry/sdk-trace-base';

const provider = new WebTracerProvider({
  sampler: new TraceIdRatioBasedSampler(0.1), // 10%のトレースのみ記録
  // ... 前述の resource, spanProcessors の設定
});
```

サンプリングレートはアプリケーションの規模やトラフィック量に応じて調整してください。

計装の方法は他にも用意されています。詳細はOpenTelemetryのドキュメントを参照してください。

[https://opentelemetry.io/ja/docs/languages/js/getting-started/browser/:embed:cite]
