---
Title: トレース - PythonにOpenTelemetryを導入する
Date: 2025-03-13T16:40:22+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/python
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960658
---

MackerelはOpenTelemetryの仕組み (計装)を利用してデータを取得しています。

このページではPythonのデータをMackerelに送信する方法を解説します。

[:contents]

## Python向けOpenTelemetry

OpenTelemetry には Python用のSDKが用意されています。

このSDKに加えて、DjangoやAWS用のSDKを使用すると、色々な範囲を計装できます。

[https://opentelemetry.io/ja/docs/languages/python/instrumentation/:embed:cite]

### Collectorを使用するべきか

データをMackerelに送信する際に、SDKから直接送信するだけではなく、Collectorを使うこともできます。

Collectorを使うかどうかを決める際は以下のページを参考にしてください。

[Collectorを使うかどうかの判断について](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## 導入方法

Pythonには複数のWebフレームワークやサーバーが存在しますが、このページでは `Django` と `Gunicorn` を使っている場合の導入方法を説明します。 FlaskやFastAPIなど、他のフレームワークを使っている場合もほぼ同じ方法で計装することができます。

以下のステップでMackerelトレーシング機能を導入できます。

1. パッケージ追加
2. 初期設定
3. Gunicornの設定
4. 独自の計装の追加 (任意)

### 1. パッケージ追加

以下のパッケージを使用します。

```bash
pip install opentelemetry-api \
  opentelemetry-exporter-otlp-proto-http \
  opentelemetry-instrumentation-django \
  opentelemetry-instrumentation-dbapi \
  opentelemetry-sdk
```

このコマンドでは DjangoとDBを計装をするパッケージをインストールしています。

他にもAWSやJinja、Redis用など色々なライブラリに対応したパッケージがあり、OpenTelemetryのサイトで見つけることができます。

[https://opentelemetry.io/ecosystem/registry/?language=python&component=instrumentatio:embed:cite]

### 2. 初期設定

OpenTelemetryのデータをMackerelに送信するためには、以下の項目を設定する必要があります。

* Resource
* SpanProcessor

例えば、以下のようにすると、SDKから直接Mackerelに送信することができます。

```python
import os
import socket
import MySQLdb
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.http import Compression
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.instrumentation.django import DjangoInstrumentor
from opentelemetry.instrumentation.dbapi import trace_integration
from opentelemetry.sdk.resources import Resource
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor, ConsoleSpanExporter
from opentelemetry.semconv.resource import ResourceAttributes

def instrument():
    tracer_provider = TracerProvider(resource=Resource.create({
        ResourceAttributes.SERVICE_NAME: "acme_service",
        ResourceAttributes.SERVICE_VERSION: "vX.Y.Z",
        ResourceAttributes.DEPLOYMENT_ENVIRONMENT: "production",
        ResourceAttributes.HOST_NAME: socket.gethostname(),
        ResourceAttributes.PROCESS_PID: os.getpid(),
    }))
    tracer_provider.add_span_processor(
        # デバッグ時にはConsoleSpanExporterが便利
        # BatchSpanProcessor(ConsoleSpanExporter())

        BatchSpanProcessor(OTLPSpanExporter(
            endpoint="https://otlp-vaxila.mackerelio.com/v1/traces",
            headers={
              "Mackerel-Api-Key": os.environ["MACKEREL_API_KEY"],
              "Accept": "*/*",
            },
            compression=Compression.Gzip
        ))
    )
    trace.set_tracer_provider(tracer_provider)

    DjangoInstrumentor().instrument()
    trace_integration(MySQLdb, "connect", "mysql")
```

この例では次の項目を設定しています。

* Resource
  * TracerProviderのResourceを設定することで、データがどこから来たかわかるようになります。
* Endpoint
  * `https://otlp-vaxila.mackerelio.com/v1/traces` と設定することで、データを直接Mackerelに送信するようになります。
  * Collectorを利用する場合は、Collectorのエンドポイント (`http://localhost:4318/v1/traces` など) を設定してください。
* Headers
  * `Mackerel-Api-Key` と `Accept` のヘッダーを設定することでMackerelと通信することができます。
  * `Mackerel-Api-Key` には、Mackerelで発行された書き込み権限のあるAPIキーを設定してください。APIキーの権限を変更した際は反映まで1分ほどお待ちください。
  * Collectorを利用する場合、ヘッダーは必要ないでしょう。
* Compression
  * データを送信する時にデータの圧縮方法を指定することができます。指定しなかった場合は圧縮されません。

### 3. Gunicornの設定

`Gunicorn` のようにプロセスをフォークして動くサーバーの場合、フォークした後にOpenTelemetryを設定する必要があります。

そのため、 `gunicorn.conf.py` などGunicornの設定ファイルで先ほど作成した関数を呼び出します。

```python
def post_fork(server, worker):
    instrument()
```

`uWSGI` を使っている場合は `@postfork` の中で呼び出します。

```python
@postfork
def init_tracing():
    instrument()
```

ちなみに、Djangoの`manage.py` を使用している場合は `main` の中で呼び出せば十分です。

```python
def main():
    """Run administrative tasks."""
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'message.settings')

    # ↓の行を追加
    instrument()

    try:
        from django.core.management import execute_from_command_line
    ...
```

### 4. 独自の計装の追加 (任意)

独自の計装を追加することで、任意の範囲を計装することができます。

計装によって、変数の値や処理時間を記録することが出来るようになります。

具体的には、下のように `start_as_current_span` を使うと計装が追加できます。

```python
tracer = trace.get_tracer("my.tracer.name")

def awesome_action(id: int):
    with tracer.start_as_current_span("awesome_action") as span:
        span.set_attribute("id", id)

        # ... 既存の処理
```

計装の方法は他にも用意されています。詳細はOpenTelemetryのドキュメントを参照してください。

[https://opentelemetry.io/ja/docs/languages/python/instrumentation/:embed:cite]
