---
Title: RubyのアプリケーションからMackerelにトレースを送信する
Date: 2025-03-13T16:40:53+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/ruby
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960676
---

このページでは、RubyのアプリケーションからMackerelにトレースを送信する方法を解説します。

[:contents]

## 概要

MackerelはOpenTelemetryの仕組み（計装）を利用してトレースを取得します。Ruby on RailsやActiveRecordなど、主要なフレームワークやライブラリ向けに提供された[計装ライブラリ](https://opentelemetry.io/ja/docs/languages/ruby/libraries/)を使用することで、既存の実装を大きく変更せずにトレースを送信できます。

RubyにはいくつかのWebフレームワークが存在しますが、このページではRuby on Railsへの導入方法を説明します。

<div class="note">
<p>📝 補足</p>
<p>提供されている計装ライブラリは<a href="https://opentelemetry.io/ecosystem/registry/?language=ruby&component=instrumentation">レジストリ</a>で検索できます。お使いのフレームワークやアプリケーション向けの計装ライブラリが存在しない場合は、独自に計装することもできます。詳しくはOpenTelemetry公式ドキュメントのRubyの<a href="https://opentelemetry.io/ja/docs/languages/ruby/instrumentation/">計装</a>ページをご確認ください。</p>
</div>

## 動作要件

RubyにおけるOpenTelemetryの[動作要件](https://opentelemetry.io/ja/docs/languages/ruby/getting-started/#prerequisites)として、以下がインストールされている必要があります。

- 下記のいずれか
  - CRuby3.1以上
  - JRuby9.3.2.0以上
  - TruffleRuby22.1以上
- Bundler

## 導入方法

アプリケーションからMackerelへトレースを送信するために、以下をおこないます。

1. Gemの追加
2. initializersでの初期設定
3. エラーの捕捉（任意）

### 1. Gemの追加

以下のGemを使用すると、Ruby on RailsへのリクエストやDBへのクエリなどが自動で計装されます。

```ruby
gem 'opentelemetry-sdk'
gem 'opentelemetry-exporter-otlp'
gem 'opentelemetry-instrumentation-all'
```

また、`opentelemetry-instrumentation-all`は多くのGemを内包しているため、下のように個別のGemだけを利用することもできます。

```ruby
gem 'opentelemetry-instrumentation-active_support'
gem 'opentelemetry-instrumentation-active_record'
gem 'opentelemetry-instrumentation-rack'
gem 'opentelemetry-instrumentation-rails'
```

### 2. initializersでの初期設定

initializerでトレースを送信するための設定をおこないます。以下の内容を`config/initializers/opentelemetry.rb`として配置し、アプリケーションを起動するとトレースが送信されます。

```ruby
require 'socket'
require 'opentelemetry/sdk'
require 'opentelemetry/exporter/otlp'
require 'opentelemetry/instrumentation/all'

OpenTelemetry::SDK.configure do |c|
  c.service_name = "my-sample-app"
  c.service_version = "vX.Y.Z"
  c.resource = OpenTelemetry::SDK::Resources::Resource.create(
    OpenTelemetry::SemanticConventions::Resource::DEPLOYMENT_ENVIRONMENT => Rails.env.to_s,
    OpenTelemetry::SemanticConventions::Resource::HOST_NAME => Socket.gethostname
  )

  # exporterの設定
  exporter = OpenTelemetry::Exporter::OTLP::Exporter.new(
    endpoint: "https://otlp-vaxila.mackerelio.com/v1/traces",
    headers: {
      "Accept" => "*/*",
      "Mackerel-Api-Key" => ENV["MACKEREL_APIKEY"]
    }
  )
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

- `service_name`はトレースのサービス名（`service.name`属性の値）になります。
- `service_version`にはバージョン情報を記載できます（任意）。
- `resource`にはOpenTelemetryの[セマンティック規約](https://opentelemetry.io/docs/specs/semconv/resource/)に基づいた属性を指定できます。トレースの送信元を詳細に識別できるようになります（任意）。
  - `DEPLOYMENT_ENVIRONMENT`：デプロイ環境（development、staging、productionなど）を指定します。この例ではRailsの環境名（`Rails.env`）を使用しています。
  - `HOST_NAME`：アプリケーションが動作しているホスト名を指定します。この例では`Socket.gethostname`でホスト名を自動取得しています。
  - その他の属性については[OpenTelemetry::SemanticConventions::Resource](https://open-telemetry.github.io/opentelemetry-ruby/opentelemetry-semantic_conventions/v1.8.0/OpenTelemetry/SemanticConventions/Resource.html)をご確認ください。
- `exporter`
  - `endpoint`はトレースの送信先の指定です。
    - Mackerelに直接送信する場合は`https://otlp-vaxila.mackerelio.com/v1/traces`を指定します。
    - Collectorを利用して送信する場合は`http://<Collectorのアドレス:ポート>/v1/traces`を指定します。
  - `${MACKEREL_APIKEY}`はMackerelのAPIキーの指定です。[APIキーの一覧](https://mackerel.io/my?tab=apikeys)から、Write権限のあるAPIキーをアプリケーションが動作するシステム内の環境変数に定義してください。
    - 環境変数ではなくAPIキーを直接記述しても動作します。
  - `ConsoleSpanExporter`を指定するとトレースが標準出力に出力されます。

### 3. エラーの捕捉（任意）

Mackerelはエラーをトラッキングできますが、RubyのOpenTelemetryは初期状態ではエラー内容を送信しません。

エラー内容を送信するには、以下のどちらかの方法でエラーを捕捉し、スパンに記録する必要があります。

1. `rescue_from`でエラーを捕捉する
2. Middlewareでエラーを捕捉する

例えば、`rescue_from`を使用する場合は以下のようにします。

```ruby
class ApplicationController < ActionController::API
  rescue_from Exception do |e|
    OpenTelemetry::Trace.current_span.record_exception(e)

    raise e
  end
end
```

## トレースを確認する

送信されたトレースは以下の手順で確認できます。

1. メニューの「[APM](https://mackerel.io/my/apm)」を選択<br>
2. サービス名を選択
  [f:id:mackerelio:20251224180534j:plain]
3. 「トレース」タブを選択
  [f:id:mackerelio:20251224180530j:plain]
4. トレース一覧からトレースを選択すると詳細が確認できます
  [f:id:mackerelio:20251224180525p:plain]

以上、RubyアプリケーションにOpenTelemetryを導入して、Mackerelへトレースを送信する方法のご紹介でした。
