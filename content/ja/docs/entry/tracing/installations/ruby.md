---
Title: トレース - RubyにOpenTelemetryを導入する
Date: 2025-03-13T16:40:53+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/ruby
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960676
---

MackerelはOpenTelemetryの仕組み (計装)を利用してデータを取得しています。

このページではRuby のデータをMackerelに送信する方法を解説します。

[:contents]

## Ruby向けOpenTelemetry

OpenTelemetry には Ruby用のSDKが用意されています。

このSDKに加えて、RubyOnRailsやActiveRecord用のSDKを使用すると、既存の実装を変更せずに計装することができます。

[https://opentelemetry.io/ja/docs/languages/ruby/instrumentation/:embed:cite]

### Collectorを使用するべきか

データをMackerelに送信する際に、SDKから直接送信するだけではなく、Collectorを使うこともできます。

Collectorを使うかどうかを決める際は以下のページを参考にしてください。

[Collectorを使うかどうかの判断について](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## 導入方法

Rubyにはいくつかのフレームワークが存在しますが、このページでは `Ruby on Rails` を使っている場合の導入方法を説明します。

以下のステップでMackerelトレーシング機能を導入できます。

1. Gemの追加
2. initializers 内での初期設定
3. エラーを捕捉
4. 独自の計装の追加 (任意)

### 1. Gemの追加

以下のGemを使用すると、Ruby on RailsへのリクエストやDBへのクエリなどが自動で計装されます。

```ruby
gem 'opentelemetry-sdk'
gem 'opentelemetry-exporter-otlp'
gem 'opentelemetry-instrumentation-all'
```

また、 `opentelemetry-instrumentation-all` は多くのGemを内包しているため、下のように個別のGemだけを利用することもできます。

```ruby
gem 'opentelemetry-instrumentation-active_support'
gem 'opentelemetry-instrumentation-active_record'
gem 'opentelemetry-instrumentation-rack'
gem 'opentelemetry-instrumentation-rails'
```

足りない場合は、OpenTelemetryのページからGemを探すことができます。

[https://opentelemetry.io/ecosystem/registry/?language=ruby&component=instrumentation:embed:cite]

### 2. initializers 内での初期設定

OpenTelemetryのデータをMackerelに送信するためには、initializer内で設定をする必要があります。

`OpenTelemetry::SDK.configure` 内では、以下の項目を設定をすることができます。

* service\_name
* service\_version
* resource
* span\_processor

例えば、 `config/initializers/opentelemetry.rb` で以下のように設定するとSDKから直接Mackerelに送信することができます。

```ruby
require 'opentelemetry/sdk'
require 'opentelemetry/exporter/otlp'
require 'opentelemetry/instrumentation/all'

OpenTelemetry::SDK.configure do |c|
  c.service_name = "acme_service"
  c.service_version = "vX.Y.Z"
  c.resource = OpenTelemetry::SDK::Resources::Resource.create(
    OpenTelemetry::SemanticConventions::Resource::DEPLOYMENT_ENVIRONMENT => Rails.env.to_s,
    OpenTelemetry::SemanticConventions::Resource::HOST_NAME => Socket.gethostname
  )

  exporter = OpenTelemetry::Exporter::OTLP::Exporter.new(
    endpoint: "https://otlp-vaxila.mackerelio.com/v1/traces",
    headers: {
      "Accept" => "*/*",
      "Mackerel-Api-Key" => ENV["MACKEREL_API_KEY"]
    }
  )
  # デバッグ時にはConsoleSpanExporterが便利
  # exporter = OpenTelemetry::SDK::Trace::Export::ConsoleSpanExporter.new
  
  c.add_span_processor(
    OpenTelemetry::SDK::Trace::Export::BatchSpanProcessor.new(exporter)
  )

  # c.use_all を使用すると使用するGemが自動で決定されます。
  c.use_all

  # c.use(...) を使用して計装対象を明示的に指定することもできます。
  # c.use('OpenTelemetry::Instrumentation::ActiveRecord')
  # c.use('OpenTelemetry::Instrumentation::Mysql2')
  # c.use('OpenTelemetry::Instrumentation::Rails')
  # ...
end
```

この例では次の項目を設定しています。

* service\_name, service\_version, resource
  * これらの項目を設定することでデータがどこから来たかわかるようになります。
* endpoint
  * `https://otlp-vaxila.mackerelio.com/v1/traces` と設定することで、データを直接Mackerelに送信するようになります。
  * Collectorを利用する場合は、Collectorのエンドポイント (`http://localhost:4318/v1/traces` など)を設定してください。
* headers
  * `Mackerel-Api-Key` と `Accept` のヘッダーを設定することでMackerelと通信することができます。
  * `Mackerel-Api-Key` には、Mackerelで発行された書き込み権限のあるAPIキーを設定してください。APIキーの権限を変更した際は反映まで1分ほどお待ちください。
  * Collectorを利用する場合、ヘッダーは必要ないでしょう。

### 3. エラーを捕捉

Mackerelはエラーをトラッキングすることができますが、RubyのOpenTelemetryはインストールしただけではエラー内容を送信しません。

エラー内容も送信するため、以下のどちらかの作業が必要です。

1. `rescue_from` でエラーを捕捉する
2. Middlewareでエラーを捕捉する

エラーの捕捉場所は変わりますが、どちらの場合であっても、捕捉したエラーをSpanに追加するだけで良いことは変わりません。

具体的に、例えば `resuce_from` を使用する場合には以下のようにします。

```ruby
class ApplicationController < ActionController
  rescue_from Exception do |e|
    OpenTelemetry::Trace.current_span.record_exception(e)

    raise e
  end
end
```

### 4. 独自の計装の追加 (任意)

独自のSpanを追加することで、任意の範囲を計装することができます。

計装によって、変数の値や処理時間を記録することが出来るようになります。

具体的には、下のように `in_span` で囲むと計装が追加できます。

```ruby
def awesome_action(arg)
  OpenTelemetry.tracer_provider.tracer.in_span("awesome_action", attributes: {"arg" => arg}) do |span|
    # ... 既存の処理
  end
end
```

計装の方法は他にも用意されています。詳細はOpenTelemetryのドキュメントを参照してください。

[https://opentelemetry.io/ja/docs/languages/ruby/instrumentation/:embed:cite]
