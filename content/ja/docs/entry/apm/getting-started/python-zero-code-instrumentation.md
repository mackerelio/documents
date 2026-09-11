---
Title: Mackerel APM導入ガイド ― Python におけるゼロコード計装
Date: 2026-09-11T11:02:18+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/python-zero-code-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293596
---

Pythonでは、`opentelemetry-instrument` コマンド経由でアプリケーションを起動するだけで、コード変更なしにトレースの収集を始められます。Flask、Django、FastAPI、requestsなど主要なフレームワーク・ライブラリに対応しており、インストール済みのライブラリを自動検出して必要な計装ライブラリをまとめてインストールする仕組みも用意されています。

[:contents]

## 前提条件

* Python 3.8以降
* pip が利用可能であること
* Mackerel OpenTelemetryコレクターがセットアップ済みであること（[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「Mackerel OpenTelemetryコレクターの準備」を参照）

## セットアップ手順

### 1. パッケージのインストール

```bash
pip install opentelemetry-distro opentelemetry-exporter-otlp
```

`opentelemetry-distro` には、SDK本体と `opentelemetry-instrument` コマンド、`opentelemetry-bootstrap` コマンドが含まれています。

### 2. 計装ライブラリの自動インストール

```bash
opentelemetry-bootstrap -a install
```

このコマンドは、現在の Python 環境にインストールされているフレームワーク・ライブラリ（Flask、Django、requests、psycopg2など）を自動的に検出し、対応する計装ライブラリをインストールします。新しいライブラリを追加した場合は、再度実行してください。

### 3. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-python-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
```


| 環境変数 | 説明 | 設定例 |
|---------|------|--------|
| `OTEL_SERVICE_NAME` | Mackerel APM上での識別名 | `my-python-app` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | コレクターの受信アドレス | `http://localhost:4318` |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | 送信プロトコル | `http/protobuf` |


### 4. アプリケーションの起動

通常の `python` コマンドの代わりに `opentelemetry-instrument` 経由で起動します。

```bash
opentelemetry-instrument python app.py
```

Gunicornなどのアプリケーションサーバーを使っている場合も同様です。

```bash
opentelemetry-instrument gunicorn myapp:app
```

### 5. 動作確認

アプリケーションにリクエストを送信し、Mackerel APMの画面でトレースが表示されることを確認します。確認方法は[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「最初のトレースを確認してみよう」を参照してください。

## 自動計装の対象

`opentelemetry-bootstrap` が検出する主なフレームワーク・ライブラリは以下のとおりです。

* **Webフレームワーク**：Flask、Django、FastAPI、Starlette、Tornado
* **HTTPクライアント**：requests、urllib3、aiohttp、httpx
* **データベース**：psycopg2、pymysql、SQLAlchemy、pymongo
* **非同期処理**：Celery、asyncio
* **その他**：Redis、Kafka、gRPC

特定のライブラリの計装を無効にしたい場合は、環境変数 `OTEL_PYTHON_DISABLED_INSTRUMENTATIONS` にライブラリ名をカンマ区切りで指定します。たとえば `OTEL_PYTHON_DISABLED_INSTRUMENTATIONS=redis,requests` のように設定します。不要な計装を無効にすることでスパン数の削減とパフォーマンスの向上が期待できます。

## よくある問題と対処法

### トレースが表示されない

* `opentelemetry-bootstrap -a install` を実行したか確認してください。計装ライブラリがインストールされていないと、トレースは生成されません
* `opentelemetry-instrument` 経由で起動しているか確認してください。`python app.py` で直接起動しても計装は有効になりません

### 仮想環境を使っている場合

仮想環境（venv、virtualenvなど）を使っている場合は、仮想環境を有効にした状態でインストールと起動を行ってください。仮想環境の外にインストールされた計装ライブラリは、仮想環境内のアプリケーションには適用されません。

## 次のステップ

* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/spring-boot-instrumentation" rel="prev">Spring Boot における OpenTelemetry 計装</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/dotnet-zero-code-instrumentation" rel="next">.NET におけるゼロコード計装</a></li>
</ul>
</nav>
