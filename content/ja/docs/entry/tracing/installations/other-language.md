---
Title: トレース - その他の言語にOpenTelemetryを導入する
Date: 2025-03-13T16:41:22+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/other-language
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960637
---

MackerelはOpenTelemetryの仕組み (計装)を利用してデータを取得しています。

このページではドキュメントでは紹介していない言語を使ってデータをMackerelに送信する方法を解説します。

[:contents]

## OpenTelemetry SDK

OpenTelemetryには多数のSDKがあり、多くの言語やフレームワークに対応しています。全てについてのドキュメントを書くことはできませんが、設定内容は共通しています。



このページでは言語共通の設定について解説します。



また、OpenTelemtryのSDKは共通部分がかなり多いため、別の言語の計装方法を解説したページも参考になります。

例えば、以下のページです。

* [GoにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/go)
* [RubyにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/ruby)

### Collectorを使用するべきか

データをMackerelに送信する際に、SDKから直接送信するだけではなく、Collectorを使うこともできます。

Collectorを使うかどうかを決める際は以下のページを参考にしてください。

[Collectorを使うかどうかの判断について](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## 導入方法

SDKの導入は以下のステップになります。

1. SDKの検索
2. アプリケーション内での設定
3. エラーを補足
4. 独自の計装の追加 (任意)

### 1. SDKの検索

導入したい言語をOpenTelemtry公式のRegistryから検索します。

以下のページで`Language` を対象言語に、 `Type` を Instrumentation に設定すると対象言語のSDKが検索できます。

[https://opentelemetry.io/ecosystem/registry:embed:cite]

### 2. アプリケーション内での設定

アプリケーション内でSDKを実行する際には、エンドポイントなどの設定が必要になります。以下の項目を設定します。

1. エンドポイント
   * SDKからデータを直接Mackerelに送信する場合は、Mackerelのエンドポイント (`https://otlp-vaxila.mackerelio.com/v1/traces`)
   * Collectorを利用する場合は、Collectorのエンドポイント (`http://localhost:4318/v1/traces` など)
2. 送信時のヘッダー
   * SDKからデータをMackerelに直接送信する場合、以下のヘッダーが必要です。
     * `Accept`： `*/*`
     * `Mackerel-Api-Key`： Mackerelで発行された書き込み権限のあるAPIキーを設定してください。APIキーの権限を変更した際は反映まで1分ほどお待ちください。
   * Collectorを利用する場合、ヘッダーは必要ありません。
3.  Resource

    最低限、以下を設定しておくといいでしょう。

    * サービス名 (`service.name`)
    * サービスのバージョン (`service.version`)
    * 環境名 (`deployment.environment.name`)

### 3. エラーの補足

残念ながら、エラーが発生した場合に自動でエラー情報を追加しないSDKもあります。

そのため、エラーが発生したときにエラーメッセージやスタックトレースをSpanに追加するようコードを変更する必要があることがあります。

使用しているSDKがエラーを補足しているかどうかは、エラーが発生したトレースの `Events` にエラー内容が書かれているかで判断できます。

エラーを補足している場合、以下のように `events` の中にエラー内容やスタックトレースが追加されます。

```json
{
  "name": "GET awesome/path",
  "context": {
    "trace_id": "0xb999451d4eba88ae77c77f829c2d6350",
    "span_id": "0x5fca4b11492b6856",
  },
  "attributes": {...},
  "events": [
    {
      "name": "exception",
      "timestamp": "2001-02-03T04:05:06.000000Z",
      "attributes": {
        "exception.type": "AwesomeException",
        "exception.message": "Error happened",
        "exception.stacktrace": "Traceback (most recent call last):\n  File \"/usr/local/lib/python3.11/site-packages/opentelemetry/trace/__init__.py\", line 573, in use_span\n...",
      }
    }
  ],
  ...
}
```

エラーを補足していない場合、フレームワークのエラーハンドリング部分など、エラーを補足している場所でOpenTelemetryのイベント(Event)を追加する必要があります。

具体的には、`recordException` や `recordError` といった関数がSDKに用意されているので、それを呼び出します。

すると、以下の属性を保持したEventがSpanに追加されます。

<table><thead><tr><th width="211.33333333333331">属性名</th><th width="185">属性の意味</th><th>例</th></tr></thead><tbody><tr><td>exception.type</td><td>エラー種別</td><td>java.net.ConnectException</td></tr><tr><td>exception.message</td><td>エラーメッセージ</td><td>Can't convert 'int' object to str implicitly…</td></tr><tr><td>exception.stacktrace</td><td>スタックトレース</td><td>Exception in thread "main" java.lang.RuntimeException: Test exception\n at <br>...</td></tr></tbody></table>

Mackerelはこれらの属性を利用してエラー内容を把握し、グルーピングしています。

### 4. 独自の計装の追加 (任意)

独自のSpanを追加することで、任意の範囲を計装することができます。

計装することで、変数の値や処理時間を記録することができます。

各言語に必ず用意されているはずです。
