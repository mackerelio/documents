---
Title: PythonのアプリケーションからMackerelにトレースを送信する
Date: 2025-03-13T16:40:22+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/python
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960658
---

このページでは、PythonのアプリケーションからトレースをMackerelに送信する方法を解説します。

[:contents]

## 概要

MackerelはOpenTelemetryの仕組み（計装）を利用してトレースを取得します。OpenTelemetryに対応したトレースはさまざまな方法で取得できますが、今回はゼロコード計装と呼ばれる、アプリケーションの実装を変更せずにトレースを送信する方法を解説します。

<div class="note">
<p>📝 補足</p>
<p>Pythonのゼロコード計装は、実行時にライブラリ関数を変更するエージェントを利用して、トレースといったテレメトリーデータを取得できるようにすることで実現されています。</p>
<p>詳しくはOpenTelemetry公式ドキュメントの <a href="https://opentelemetry.io/ja/docs/zero-code/python/">Pythonゼロコード・計装 | OpenTelemetry</a> をご確認ください。</p>
</div>

## 動作要件

OpenTelemetryの[動作要件](https://opentelemetry.io/ja/docs/languages/python/#version-support)として、Pythonが指定されたバージョン以上である必要があります。

- Python 3.9以上

## 導入方法

アプリケーションからMackerelへトレースを送信するために、以下をおこないます。

1. パッケージのインストール
2. エージェントを経由してアプリケーションを実行

### 1. パッケージのインストール

以下のコマンドで、PythonでのOpenTelemetry利用に必要なパッケージをインストールします。

```bash
pip install opentelemetry-distro opentelemetry-exporter-otlp
```

つづいて、`opentelemetry-bootstrap` コマンドを利用して、site-packages フォルダに存在するパッケージに対応する計装ライブラリをインストールします。

```bash
opentelemetry-bootstrap -a install
```

OpenTelemetryはPythonのさまざまなパッケージをサポートしており、パッケージごとに個別の計装ライブラリが用意されています。提供されているパッケージは以下のページで検索できます。

[https://opentelemetry.io/ecosystem/registry/?language=python&component=instrumentation:embed:cite]

<div class="note">
<p>📝 補足</p>
<p>お使いのフレームワークやアプリケーション向けの計測ライブラリが存在しない場合は、独自に計装することもできます。</p>
<p>詳しくはOpenTelemetry公式ドキュメントの <a href="https://opentelemetry.io/ja/docs/languages/python/instrumentation/">Instrumentation | OpenTelemetry</a>をご確認ください。</p>
</div>

### 2. エージェントを経由してアプリケーションを実行

ゼロコード計装を利用する場合、`opentelemetry-instrument` を経由してPythonアプリケーションを実行することで、アプリケーションから自動でトレースを送信できます。

環境変数 `MACKEREL_APIKEY` にMackerelのWrite権限を持つAPIキーを設定した状態で以下のように実行することで、`my-sample-app` というサービス名でMackerelにトレースが送信されます。

```sh
opentelemetry-instrument \
    --traces_exporter otlp \
    --metrics_exporter none \
    --logs_exporter none \
    --service_name my-sample-app \
    --exporter_otlp_traces_endpoint https://otlp-vaxila.mackerelio.com/v1/traces \
    --exporter_otlp_traces_headers "Mackerel-Api-Key=${MACKEREL_APIKEY},Accept='*/*'" \
    --exporter_otlp_traces_protocol http/protobuf \
    python myapp.py  # この行は起動したいアプリケーションの起動コマンドに置き換えてください
```

- `--traces_exporter` を `console` にするとトレースが標準出力に出力されます。
- `--service_name` はトレースのサービス名（`service.name`属性の値）になります。
- `--exporter_otlp_traces_endpoint` はトレースの送信先の指定です。
  - Mackerelに直接送信する場合は `https://otlp-vaxila.mackerelio.com/v1/traces` を指定します。
  - Collectorを利用して送信する場合は `http://<Collectorのアドレス:ポート>/v1/traces` を指定します。
- `${MACKEREL_APIKEY}` はMackerelのAPIキーの指定です。[APIキーの一覧](https://mackerel.io/my?tab=apikeys)から、Write権限のあるAPIキーをアプリケーションが動作するシステム内の環境変数に定義してください。
  - 環境変数ではなくAPIキーを直接記述しても動作します。

## トレースを確認する

送信されたトレースは以下の手順で確認できます。

1. メニューの「[APM](https://mackerel.io/my/apm)」を選択<br>
2. サービス名を選択
  [f:id:mackerelio:20251224180534j:plain]
3. 「トレース」タブを選択
  [f:id:mackerelio:20251224180530j:plain]
4. トレース一覧からトレースを選択すると詳細が確認できます
  [f:id:mackerelio:20251224180525p:plain]

以上、Pythonで作成されたアプリケーションにゼロコード計装をおこなって、Mackerelへトレースを投稿する方法のご紹介でした。
