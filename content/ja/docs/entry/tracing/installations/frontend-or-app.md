---
Title: トレース - フロントエンド・アプリでのエラートラッキング
Date: 2025-03-13T16:41:52+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/frontend-or-app
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333959749
---

[:contents]

## Sentryによるエラートラッキング

Mackerelトレーシング機能はOpenTelemetryを主に利用していますが、SentryのSDKを使ってエラーを収集することもできます。

SentryのSDKを利用することによって、フロントエンドやアプリなどOpenTelemetryでは収集できない場所のデータを集めることができます。

このページではSentryを使ったエラートラッキングの方法を紹介します。

** このページではReactへの導入方法を例に紹介しますが、他の言語・フレームワークの場合も同様の手順で設定できます。各言語についてはSentry SDKのページを参照してください **

## 例: Reactへの導入

以下のステップで導入できます

1. Sentry SDKのインストール
2. 送信先設定

### 1. Sentry SDKのインストール

Sentry のSDKの中から、React用のSDKを探し、インストールします。

```bash
npm install --save @sentry/react
```

### 2. 送信先設定

Sentryの初期化時、以下のように設定するとSentryからVaxilaにエラーを送信することができます。

```javascript
const clientToken = "vaxila_cli_xxxxxx"
const serviceName = "frontend"

Sentry.init({
  dsn: `https://${clientToken}@telemetry.vaxila-labs.com/sentry/v1/api/${serviceName}/0`,
  environment: "production",
  release: "v1.1.1",
  autoSessionTracking: false
});
```

この例では次の項目を設定しています。

* dsn
  * データの送信先です。サービス名とVaxilaのクライアントトークンを設定して `telemetry.vaxila-labs.com` へデータを送信するようにしています。
* environment
  * 環境の名前です。 productionやstagingなど、適切な名前を設定します。
* release
  * バージョンの名前です。git のハッシュ値やタグの名前など、適切な名前を設定します。
* autoSessionTracking
  * Sentryがセッション情報を送信するかどうかの設定です。Vaxilaはエラー以外の情報を無視するので通信量削減のため送信自体を停止するのを推奨しています。



### 全体像

以上でSentryへの対応は終わりです。

あとは完成したコードを既存のコードに追加するだけです。

例えば、TypeScriptを使用した場合、 `src/main.tsx` は以下のようになります。

```tsx
import ReactDOM from 'react-dom/client'
import * as Sentry from "@sentry/react";
import ...

const clientToken = "xxxxxx"
const serviceName = "frontend"

Sentry.init({
<strong>  dsn: `https://${clientToken}@telemetry.vaxila-labs.com/sentry/v1/api/${serviceName}/0`,
</strong>  environment: "production",
  release: "v1.1.1",
  autoSessionTracking: false
});

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  &#x3C;React.StrictMode>
    &#x3C;App />
  &#x3C;/React.StrictMode>,
)
```
