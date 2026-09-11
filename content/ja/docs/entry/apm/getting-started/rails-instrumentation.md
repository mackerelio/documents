---
Title: Mackerel APM導入ガイド ― Ruby on Rails における OpenTelemetry 計装
Date: 2026-09-11T11:02:29+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/rails-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293649
---

Ruby on Railsでは、ゼロコード計装（コード変更不要の方式）は提供されていませんが、イニシャライザーに数行のコードを追加するだけでトレースの収集を始められます。`opentelemetry-instrumentation-all` メタパッケージを使えば、Rails本体、ActiveRecord、ActiveJob、Net::HTTPなど主要なコンポーネントの計装が一括で有効になります。

[:contents]

## 前提条件

* Ruby 3.0以降、Rails 6.0以降
* Bundlerが利用可能であること
* Mackerel OpenTelemetryコレクターがセットアップ済みであること（[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「Mackerel OpenTelemetryコレクターの準備」を参照）

## セットアップ手順

### 1. Gemのインストール

`Gemfile` に以下を追加します。

```ruby
gem 'opentelemetry-sdk'
gem 'opentelemetry-exporter-otlp'
gem 'opentelemetry-instrumentation-all'
```

```bash
bundle install
```

`opentelemetry-instrumentation-all` は、Rails関連の計装ライブラリをまとめたメタパッケージです。個別に必要なライブラリだけをインストールすることもできます。

```ruby
# 個別にインストールする場合の例
gem 'opentelemetry-sdk'
gem 'opentelemetry-exporter-otlp'
gem 'opentelemetry-instrumentation-rails'
gem 'opentelemetry-instrumentation-active_record'
gem 'opentelemetry-instrumentation-active_job'
gem 'opentelemetry-instrumentation-net_http'
gem 'opentelemetry-instrumentation-pg'
```

### 2. イニシャライザーの作成

`config/initializers/opentelemetry.rb` を作成します。

```ruby
require 'opentelemetry/sdk'
require 'opentelemetry/instrumentation/all'

OpenTelemetry::SDK.configure do |c|
  c.service_name = ENV.fetch('OTEL_SERVICE_NAME', 'my-rails-app')
  c.use_all
end
```

`use_all` メソッドは、インストール済みの計装ライブラリをすべて有効にします。

### 3. 環境変数の設定と起動

```bash
export OTEL_SERVICE_NAME="my-rails-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
rails server
```


| 環境変数 | 説明 | 設定例 |
|---------|------|--------|
| `OTEL_SERVICE_NAME` | Mackerel APM上での識別名 | `my-rails-app` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | コレクターの受信アドレス | `http://localhost:4318` |


### 4. 動作確認

アプリケーションにリクエストを送信し、Mackerel APMの画面でトレースが表示されることを確認します。確認方法は[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「最初のトレースを確認してみよう」を参照してください。

## 自動計装の対象

`use_all` を使用すると、以下のコンポーネントが自動的に計装されます。

* **Rails本体**：コントローラーのアクション処理、ルーティング
* **ActiveRecord**：データベースクエリの実行
* **ActiveJob**：バックグラウンドジョブの実行
* **Action View**：テンプレートのレンダリング
* **Net::HTTP**：外部HTTPリクエスト
* **データベースアダプター**：PostgreSQL（pg）、MySQL2、SQLite3
* **Redis**：Redisコマンドの実行
* **Faraday**：HTTP クライアントリクエスト

## 個別の計装ライブラリを選択する場合

`opentelemetry-instrumentation-all` の代わりに、必要な計装ライブラリだけを個別にインストールすることもできます。イニシャライザーでは `use_all` の代わりに `use` で個別に指定します。

```ruby
OpenTelemetry::SDK.configure do |c|
  c.service_name = ENV.fetch('OTEL_SERVICE_NAME', 'my-rails-app')
  c.use 'OpenTelemetry::Instrumentation::Rails'
  c.use 'OpenTelemetry::Instrumentation::ActiveRecord'
  c.use 'OpenTelemetry::Instrumentation::Net::HTTP'
end
```

本番環境でトレースのノイズを減らしたい場合や、特定のライブラリの計装が問題を起こす場合に有効です。不要な計装を減らすことでスパン数の削減とパフォーマンスの向上が期待できます。

環境変数で制御することもできます。たとえば `OTEL_RUBY_INSTRUMENTATION_SINATRA_ENABLED=false` のように、ライブラリ名を大文字にして `OTEL_RUBY_INSTRUMENTATION_{NAME}_ENABLED=false` の形式で指定します。

## よくある問題と対処法

### トレースが表示されない

* `bundle install` を実行したか確認してください
* イニシャライザーファイル名が `opentelemetry.rb` であり、`config/initializers/` に配置されているか確認してください
* コレクターが起動しているか、`OTEL_EXPORTER_OTLP_ENDPOINT` が正しいか確認してください

### PumaやUnicornとの組み合わせ

マルチプロセスのアプリケーションサーバー（Puma、Unicorn）を使っている場合、各ワーカープロセスで計装が初期化されます。イニシャライザーはRailsの起動プロセスで自動的に読み込まれるため、追加の設定は通常不要です。

## 次のステップ

* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/dotnet-zero-code-instrumentation" rel="prev">.NET におけるゼロコード計装</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/reverse-index" rel="next">逆引きリファレンス</a></li>
</ul>
</nav>
