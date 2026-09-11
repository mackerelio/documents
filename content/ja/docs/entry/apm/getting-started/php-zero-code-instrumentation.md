---
Title: Mackerel APM導入ガイド ― PHPにおけるゼロコード計装
Date: 2026-09-11T11:02:03+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/php-zero-code-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293495
---

PHPでは、OpenTelemetry PHP拡張（ext-opentelemetry）と計装ライブラリを組み合わせることで、アプリケーションコードを変更せずにトレースの収集を始められます。Laravel、Symfony、CakePHP、Slim、PDO、cURLなど主要なフレームワーク・ライブラリに対応しています。

[:contents]

## 前提条件

* PHP 8.0以降
* PECL および Composer が利用可能であること
* Mackerel OpenTelemetry コレクターがセットアップ済みであること（[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「Mackerel OpenTelemetry コレクターの準備」を参照）

## セットアップ手順

### 1. PHP拡張のインストール

OpenTelemetry PHP拡張（ext-opentelemetry）をインストールします。この拡張がPHPランタイムにフックし、計装ライブラリがフレームワーク・ライブラリの動作を自動的に捕捉できるようにします。

```bash
pecl install opentelemetry
```

拡張を有効化するために、PHPエンジンの決められた設定場所に追加します。たとえば`/usr/local/etc/php/conf.d`が設定場所であれば、`opentelemetry.ini`ファイルとして以下を記述します。

```
[opentelemetry]
extension=opentelemetry.so
```

設定後、`php.ini` に拡張が追加されていることを確認してください。

```bash
php -m | grep opentelemetry
```

### 2. 計装ライブラリのインストール

Composerで、SDKと計装ライブラリをインストールします。

```bash
composer require open-telemetry/sdk open-telemetry/exporter-otlp
```

フレームワーク固有の計装ライブラリも追加します。

```bash
# Laravelの場合
composer require open-telemetry/opentelemetry-auto-laravel

# Symfonyの場合
composer require open-telemetry/opentelemetry-auto-symfony

# CakePHPの場合
composer require open-telemetry/opentelemetry-auto-cakephp
```

### 3. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-php-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
export OTEL_PHP_AUTOLOAD_ENABLED=true
```


| 環境変数 | 説明 | 設定例 |
|---------|------|--------|
| `OTEL_SERVICE_NAME` | Mackerel APM上での識別名 | `my-php-app` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | コレクターの受信アドレス | `http://localhost:4318` |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | 送信プロトコル | `http/protobuf` |
| `OTEL_PHP_AUTOLOAD_ENABLED` | 計装の自動読み込みを有効化 | `true` |


PHPのOTLPエクスポーターはHTTPプロトコルのみに対応しているため、コレクターの4318番ポート（HTTP）を指定します。gRPC（4317番ポート）は使用できません。

### 4. アプリケーションの起動

通常どおりアプリケーションを起動します。`OTEL_PHP_AUTOLOAD_ENABLED=true` が設定されていれば、Composerのオートローダー経由で計装が自動的に有効になります。

```bash
php artisan serve  # Laravelの場合
php -S localhost:8000  # ビルトインサーバーの場合
```

### 5. 動作確認

アプリケーションにリクエストを送信し、Mackerel APMの画面でトレースが表示されることを確認します。確認方法は[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「最初のトレースを確認してみよう」を参照してください。

## 自動計装の対象

以下のフレームワーク・ライブラリに対応する計装パッケージが提供されています。

* **Webフレームワーク**：Laravel、Symfony、CakePHP、Slim、WordPress
* **HTTPクライアント**：cURL、Guzzle
* **データベース**：PDO、MySQLi、MongoDB
* **キャッシュ・キューイング**：Redis（Predis）
* **ロギング**：Monolog

## よくある問題と対処法

### トレースが表示されない

* `php -m | grep opentelemetry` でPHP拡張がロードされているか確認してください
* `OTEL_PHP_AUTOLOAD_ENABLED=true` が設定されているか確認してください。この環境変数がないと計装は有効になりません
* `OTEL_EXPORTER_OTLP_ENDPOINT` がHTTPポート（4318）を指しているか確認してください。PHPではgRPC（4317）は使用できません

### PHP-FPMを使っている場合

PHP-FPM環境では、環境変数を `php-fpm.conf` または各プールの設定ファイルで指定する必要があります。

```ini
; /etc/php-fpm.d/www.conf
env[OTEL_SERVICE_NAME] = my-php-app
env[OTEL_EXPORTER_OTLP_ENDPOINT] = http://localhost:4318
env[OTEL_EXPORTER_OTLP_PROTOCOL] = http/protobuf
env[OTEL_PHP_AUTOLOAD_ENABLED] = true
```

設定変更後はPHP-FPMの再起動が必要です。

### Composerのオートローダーが見つからない

`OTEL_PHP_AUTOLOAD_ENABLED=true` は、Composerのオートローダー（`vendor/autoload.php`）が読み込まれるタイミングで計装を初期化します。Composerを使っていないプロジェクトでは、手動でSDKを初期化する必要があります。

## 次のステップ

* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/nodejs-zero-code-instrumentation" rel="prev">Node.js におけるゼロコード計装</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/java-agent-instrumentation" rel="next">Java Agent によるゼロコード計装</a></li>
</ul>
</nav>
