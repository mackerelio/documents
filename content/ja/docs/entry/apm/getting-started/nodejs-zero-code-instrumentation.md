---
Title: Mackerel APM導入ガイド ― Node.js におけるゼロコード計装
Date: 2026-09-11T11:01:58+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/nodejs-zero-code-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293472
---

Node.jsでは、`@opentelemetry/auto-instrumentations-node` パッケージを `--require` オプションで読み込ませるだけで、アプリケーションコードを変更せずにトレースの収集を始められます。Express、Fastify、Koa、http/https、pg、mysql、mongodbなど主要なフレームワーク・ライブラリが自動的に計装されます。

[:contents]

## 前提条件

* Node.js 14以降
* npm または yarn が利用可能であること
* Mackerel OpenTelemetryコレクターがセットアップ済みであること（[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「Mackerel OpenTelemetryコレクターの準備」を参照）

## セットアップ手順

### 1. パッケージのインストール

```bash
npm install @opentelemetry/auto-instrumentations-node
```

このパッケージには、Node.js向けの計装ライブラリ一式と、ゼロコード計装に必要なセットアップコードが含まれています。

### 2. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-node-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
```


| 環境変数 | 説明 | 設定例 |
|---------|------|--------|
| `OTEL_SERVICE_NAME` | Mackerel APM上での識別名 | `my-node-app` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | コレクターの受信アドレス | `http://localhost:4318` |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | 送信プロトコル | `http/protobuf` |


`OTEL_EXPORTER_OTLP_ENDPOINT` は、Mackerel OpenTelemetryコレクターが稼働しているアドレスを指定します。コレクターをデフォルト設定で起動している場合は `http://localhost:4318` です。

### 3. アプリケーションの起動

通常の起動コマンドに `--require` オプションを追加して、計装ライブラリを読み込ませます。

```bash
node --require @opentelemetry/auto-instrumentations-node/register app.js
```

`--require` オプションにより、アプリケーションコードが実行される前に計装ライブラリがロードされ、対応するフレームワーク・ライブラリの動作が自動的にフックされます。

TypeScriptを `ts-node` で実行している場合も同様です。

```bash
node --require @opentelemetry/auto-instrumentations-node/register -r ts-node/register app.ts
```

### 4. 動作確認

アプリケーションにリクエストを送信し、Mackerel APMの画面でトレースが表示されることを確認します。確認方法は[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「最初のトレースを確認してみよう」を参照してください。

## 自動計装の対象

`@opentelemetry/auto-instrumentations-node` は、以下のライブラリを自動的に計装します。

* **Webフレームワーク**：Express、Fastify、Koa、Hapi、Restify
* **HTTPクライアント/サーバー**：http、https、undici（fetch）
* **データベース**：pg（PostgreSQL）、mysql、mysql2、mongodb、redis、ioredis
* **ORM**：Knex、Sequelize、Mongoose、Prisma
* **メッセージング**：amqplib（RabbitMQ）、kafkajs
* **gRPC**：@grpc/grpc-js
* **GraphQL**：graphql
* **その他**：dns、net、fs（ファイルシステム操作）

不要な計装を無効にしたい場合は、環境変数 `OTEL_NODE_DISABLED_INSTRUMENTATIONS` にライブラリ名をカンマ区切りで指定します。たとえばファイルシステムとDNSの計装を無効にするには `OTEL_NODE_DISABLED_INSTRUMENTATIONS=fs,dns` と設定します。逆に、特定のライブラリだけを有効にしたい場合は `OTEL_NODE_ENABLED_INSTRUMENTATIONS` を使います。不要な計装を無効にすることでスパン数の削減とパフォーマンスの向上が期待できます。

## ESMモジュール（import構文）を使っている場合

ESM（ECMAScript Modules）を使っているアプリケーションでは、`--require` の代わりに `--import` を使用します。

```bash
node --import @opentelemetry/auto-instrumentations-node/register app.mjs
```

`package.json` に `"type": "module"` が指定されている場合も同様です。

## よくある問題と対処法

### トレースが表示されない

* `--require`（または `--import`）オプションが正しく指定されているか確認してください。`node app.js` で直接起動した場合、計装は有効になりません
* コレクターが起動しているか、`OTEL_EXPORTER_OTLP_ENDPOINT` が正しいか確認してください

### 特定のライブラリが計装されない

* 計装ライブラリが対応しているバージョン範囲外の場合、計装されないことがあります。対応バージョンはOpenTelemetry JavaScript Contribのリポジトリで確認できます
* ネイティブの `fetch`（Node.js 18以降）は `undici` 経由の計装で対応しています

### 起動時に警告が出る

`@opentelemetry/auto-instrumentations-node` は多くの計装ライブラリを含むため、インストールされていないライブラリに対して警告が出る場合があります。アプリケーションの動作には影響しません。

## 次のステップ

* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/production-readiness-checklist" rel="prev">Mackerel APM 本番導入チェックリスト</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/php-zero-code-instrumentation" rel="next">PHPにおけるゼロコード計装</a></li>
</ul>
</nav>
