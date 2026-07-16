---
Title: Mackerel にログを送信する
Date: 2026-07-16T11:15:21+09:00
URL: https://mackerel.io/ja/docs/entry/log/sending
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032054079012
---

このヘルプでは、アプリケーションやミドルウェアのログを OpenTelemetry（OTel）経由で Mackerel に送信し、ログ画面に表示させるまでの手順を解説します。

<div class="note">
    <p>Mackerelのログ機能は現在β版として提供しています。以下の点についてはあらかじめご了承ください。</p>
</div>

- 今後、仕様や画面デザインが変更される可能性があります。
- 機能改善や不具合修正のために、臨時のメンテナンスを実施する場合があります。
- お預かりしているログデータの保持を保証することはできません。

**目次**

[:contents]

## 導入

Mackerel のログ機能では、**サービス単位**でログを横断的に検索し、チームで検索ノウハウを共有できます。「どこにどんなログがあるのか分からない」「ログの調べ方が属人化している」といった課題を解消するための基盤となる機能です。

その価値を活かすための第一歩が、ここで説明するログの送信設定です。このヘルプでは、以下を達成します。

* アプリケーションのログを Mackerel のログ画面に表示させる
* サードパーティアプリケーション（Nginx やデータベースなど）のログを Mackerel に送信する

普段使っているロガー（slog など）はそのまま活かせます。**OTel ログブリッジ**というアダプターを接続し、**OpenTelemetryコレクター（以下、コレクター）**を経由して Mackerel に送信する構成です。既存のログ出力コードを変更する必要はほぼありません。

## ログ送信の全体像

どの方法でも、最終的に**コレクター**を経由して Mackerel にログを送信する点は共通です。アプリケーションからコレクターにログを届ける方法によって、構成が異なります。

##### ログブリッジ経由の場合

アプリケーション内のロガーに OTel ログブリッジを接続し、OTel SDK 経由でコレクターに送信します。

[f:id:mackerelio:20260706123724p:plain]

##### ログファイル経由の場合

アプリケーションやミドルウェアがファイルに出力したログを、コレクターの Filelog Receiver で読み取って送信します。

[f:id:mackerelio:20260706123553p:plain]

主なコンポーネントの役割は以下のとおりです。

* **ロガー**：アプリケーションコード内で普段使っているログ出力（slog など）
* **OTel ログブリッジ**：既存ロガーの出力を OpenTelemetry 形式に変換するアダプター。ロガーの出力コードを変更せずに OTel 形式へ橋渡しする
* **OTel SDK（ログプロバイダー）**：ログを収集し、エクスポーターを通じてコレクターへ送信する基盤
* **コレクター**：ログを受け取り（OTLP での受信や Filelog Receiver でのファイル読み取りなど）、バッチ処理やフィルタリングを行って、Mackerel へ転送するゲートウェイ

いずれの方法でも、主な作業はコレクターの設定と、アプリケーション側の接続設定です。

なお、コレクターを経由せず、アプリケーションから Mackerel に直接ログを送信することも可能です。しかし、ログにはコスト削減のための不要な情報のフィルタリングや、機微情報のマスキングといった要件が発生しやすいため、それらの処理を担うコレクターを間に置く構成をお勧めします。

## どの方法でログを送信するか

ログの送信方法は、アプリケーションの言語・フレームワークやログの出力方式によって異なります。ここでは、自社開発のアプリケーション（ファーストパーティ）を対象に、主なパターンを紹介します。ご利用の環境に当てはまるものを選んでください。

<!-- 図解: ログ送信方法の選択。必要に応じて視覚的な図に差し替える -->

* **ライブラリ計装がある場合**：ログを有効にするだけで送信できる
* **利用しているログライブラリのブリッジがある場合**（slog など）：既存ライブラリにブリッジを挟み込む形で微修正する
* **ログをファイルに書き出している場合**：Filelog Receiver でファイルから OTel 形式に変換する
* **コンテナ環境でログを標準出力に出している場合**：Fluent Bit や Fluentd でログを収集しておき、Fluent Forward Receiver で OTel 形式に変換する
* **送信時のオーバーヘッドを最小にしたい場合**：Log SDK を利用して実装する
* **上記のいずれにも当てはまらない場合**：OTel の仕様に沿って Log SDK を自作する

このヘルプでは、OTel ログブリッジ経由の手順を Go + slog を例に解説します。サードパーティアプリケーション（Nginx やデータベースなど）のログ送信についてはセクション「サードパーティアプリケーションのログを送信する」を参照してください。

## 前提条件（OTel ログブリッジ経由）

ログ送信を始める前に、以下を準備してください。

* Mackerel のユーザーアカウントとオーガニゼーション
* Write 権限のある APIキー（Mackerel の Webコンソールから取得）
* コレクターを動作させる環境（このヘルプの起動例では Docker を使用していますが、Docker は必須ではありません）

<!-- キャプチャ: Mackerel Webコンソールの APIキー取得画面。APIキータブが選択され、Write権限のあるAPIキーが表示されている状態 -->

このヘルプでは Go をサンプルとして使用します。以下の条件を満たしていることを確認してください。

* Go 1.22 以上

## コレクターを準備する

コレクターの設定は言語によらず共通です。アプリケーション側の設定を始める前に、まずコレクターを準備しましょう。

### コレクターの選択

ログ送信に利用できるコレクターは2種類あります。

* **OpenTelemetry Collector Contrib**：コミュニティ提供の汎用コレクター。Mackerel 以外のバックエンドにも送信でき、柔軟な構成が可能
* **Mackerel Distro of OpenTelemetry（MDOT）コレクター**：Mackerel が提供する専用コレクター。Mackerel への送信に最適化されている

どちらでもログ送信は可能です。このヘルプでは両方の設定例を示します。

### 設定ファイル（config.yaml）の作成

コレクターの動作は `config.yaml` で定義します。ログパイプラインを構成するために、受信（receivers）、処理（processors）、送信（exporters）、パイプライン（service.pipelines）を設定します。

##### OpenTelemetry Collector Contrib の場合

以下は、OpenTelemetry Collector Contrib でアプリケーションからログを受信し、Mackerel に送信する設定例です。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: "0.0.0.0:4317"
      http:
        endpoint: "0.0.0.0:4318"

exporters:
  otlp_http/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": "${env:MACKEREL_APIKEY}"
    sending_queue:
      batch:
        max_size: 3500000
        sizer: bytes

service:
  pipelines:
    logs:
      receivers: [otlp]
      exporters: [otlp_http/mackerel]
```

設定のポイントは以下のとおりです。

* **receivers**：OTLP プロトコルで gRPC（`0.0.0.0:4317`）と HTTP（`0.0.0.0:4318`）の両方でログを受信します
* **exporters**：`otlp_http` で Mackerel のエンドポイント（`https://otlp-vaxila.mackerelio.com`）にログを送信します。`Mackerel-Api-Key` ヘッダーに Write 権限のある APIキーを設定してください。`sending_queue.batch` でバッチ処理を行い、送信効率を高めます
* **service.pipelines**：`logs` パイプラインで、受信したログを Mackerel に送信する流れを定義します

##### Mackerel OpenTelemetry（MDOT）コレクターの場合

MDOT コレクターを利用する場合は、以下のように設定します。Mackerel 向けのエクスポーターが組み込まれているため、エンドポイントや認証ヘッダーの設定がシンプルになります。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: "0.0.0.0:4317"
      http:
        endpoint: "0.0.0.0:4318"

exporters:
  mackerel_otlp:

service:
  pipelines:
    logs:
      receivers: [otlp]
      exporters: [mackerel_otlp]
```

### コレクターの起動

Docker を使ってコレクターを起動します。以下は OpenTelemetry Collector Contrib の例です。

```bash
docker run --rm \
  -v $(pwd)/config.yaml:/etc/otelcol-contrib/config.yaml \
  -p 4317:4317 \
  -p 4318:4318 \
  -e MACKEREL_APIKEY=<your-api-key> \
  otel/opentelemetry-collector-contrib:latest
```

`<your-api-key>` は Mackerel の Webコンソールで取得した APIキーに置き換えてください。

MDOT コレクターを利用する場合は、以下のようになります。

```bash
docker run --rm \
  -v $(pwd)/config.yaml:/home/nonroot/config.yaml \
  -p 4317:4317 \
  -p 4318:4318 \
  -e MACKEREL_APIKEY=<your-api-key> \
  mackerel/otelcol-mackerel:latest --config /home/nonroot/config.yaml
```

起動後、以下のようなログが出力されれば正常に動作しています。

```
... "msg":"Everything is ready. Begin running and processing data."
```

## Go（slog）からログを送信する

Go の標準ログライブラリ `slog` を使い、OTel ログブリッジ経由でログを送信する方法を解説します。

### 必要なパッケージ

以下のパッケージをインストールします。

```bash
go get go.opentelemetry.io/otel \
       go.opentelemetry.io/otel/sdk/log \
       go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploghttp \
       go.opentelemetry.io/contrib/bridges/otelslog
```

各パッケージの役割は以下のとおりです。

* `go.opentelemetry.io/otel`：OpenTelemetry の API
* `go.opentelemetry.io/otel/sdk/log`：ログプロバイダー（ログの収集・送信基盤）
* `go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploghttp`：HTTP でコレクターにログを送信するエクスポーター
* `go.opentelemetry.io/contrib/bridges/otelslog`：slog の出力を OTel 形式に変換するログブリッジ

### OTel ログプロバイダーのセットアップ

ログエクスポーターとログプロバイダーを作成します。
以下は簡単なWebアプリケーションにセットアップした例です。

```go
package main

import (
	"context"
	"fmt"
	"log/slog"
	"net/http"
	"os"

	"go.opentelemetry.io/contrib/bridges/otelslog"
	"go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploghttp"
	"go.opentelemetry.io/otel/log/global"
	"go.opentelemetry.io/otel/sdk/log"
	"go.opentelemetry.io/otel/sdk/resource"
)

func main() {
	ctx := context.Background()

	// ロガープロバイダーを作成します
	// ブリッジを作成するときに、このインスタンスを直接渡すことができます
	loggerProvider, err := newLoggerProvider(ctx)
	if err != nil {
		panic(err)
	}

	// 何もリークしないようにシャットダウンを適切に処理します
	defer func() {
		if err := loggerProvider.Shutdown(ctx); err != nil {
			fmt.Println(err)
		}
	}()

	// global.LoggerProviderにアクセスできるように、グローバルロガープロバイダーとして登録します
	// ほとんどのログブリッジは、デフォルトとしてグローバルロガープロバイダーを使用します
	// グローバルロガープロバイダーが設定されていない場合、no-op実装が使用され、
	// データの生成に失敗します
	global.SetLoggerProvider(loggerProvider)

	// slog に OTel ログブリッジを接続
	slog.SetDefault(slog.New(otelslog.NewHandler("sample-app")))

	// 以降はアプリケーションの処理
	// slog 呼び出しは自動的に OTel 形式に変換される
	http.HandleFunc("/error/", func(w http.ResponseWriter, r *http.Request) {
		slog.ErrorContext(r.Context(), "error endpoint called")
		http.Error(w, "Internal Server Error", http.StatusInternalServerError)
	})

	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		slog.InfoContext(r.Context(), "request received")
		fmt.Fprint(w, "Hello, World!")
	})

	slog.InfoContext(ctx, "server listening on http://localhost:8080")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		slog.ErrorContext(ctx, "server failed", slog.String("error", err.Error()))
		os.Exit(1)
	}
}

// ロガープロバイダーの設定
func newLoggerProvider(ctx context.Context) (*log.LoggerProvider, error) {
	exporter, err := otlploghttp.New(ctx)
	if err != nil {
		return nil, err
	}
	processor := log.NewBatchProcessor(exporter)
	provider := log.NewLoggerProvider(
		log.WithResource(resource.Default()),
		log.WithProcessor(processor),
	)
	return provider, nil
}
```

### slog に OTel ログブリッジを接続する

上のコード例で示したように、`global.SetLoggerProvider` でグローバルロガープロバイダーを登録したあと、`otelslog.NewHandler` でブリッジハンドラーを作成し、`slog.SetDefault` でデフォルトロガーに設定します。

```go
global.SetLoggerProvider(loggerProvider)
slog.SetDefault(slog.New(otelslog.NewHandler("sample-app")))
```

グローバルロガープロバイダーが登録済みの場合、`otelslog.NewHandler` は自動的にそれを使用します。この設定以降、`slog.Info("message")` や `slog.Error("error occurred")` の呼び出しが自動的に OTel 形式に変換され、コレクター経由で Mackerel に送信されます。

### 動作確認

アプリケーションを起動し、Mackerel の Webコンソールでログが表示されることを確認しましょう。

1. コレクターが起動していることを確認
2. アプリケーションを起動してログを出力
  - 実行の際に、以下の環境変数を設定して、ログの送信先や`service.name`属性を付与します。
    - `OTEL_SERVICE_NAME=my-sample-app`
    - `OTEL_EXPORTER_OTLP_LOGS_ENDPOINT=http://localhost:4318/v1/logs`
  - `curl http://localhost:8080/` や `curl http://localhost:8080/error/` でアクセスし、ログを発生させます
3. Mackerel の Webコンソールでログ画面を開く
4. 「ログの検索を開始」をクリック
5. サービス名（`service.name` に設定した値）を選択
6. ログレベルを「すべて」にした状態で「検索」ボタンをクリック
7. 送信したログが表示されていることを確認

<div class="note">
<p><code>service.name</code> リソース属性（上記の例では、環境変数 <code>OTEL_SERVICE_NAME</code> に設定した <code>"my-sample-app"</code>）は、Mackerel のログ画面でサービスを選択する際の名前に対応します。ログを検索するときにこのサービス名で絞り込むため、チームで認識しやすい名前を設定してください。</p>
</div>

<figure class="figure-image figure-image-fotolife" title="slog から OTel ログブリッジ経由で送信されたログ">[f:id:mackerelio:20260714224733p:plain]<figcaption>slog から OTel ログブリッジ経由で送信されたログ</figcaption></figure>

##### ログが表示されない場合のチェックポイント

ログが Mackerel に表示されない場合は、以下の点を確認してください。

* **コレクターが起動しているか**：Docker コンテナが出力するログに `Everything is ready` のメッセージが出ているか確認してください
* **エンドポイントに到達できるか**：アプリケーションからコレクターのアドレス（`http://localhost:4318`）にアクセスできるか確認してください。Docker のネットワーク設定によっては `http://host.docker.internal:4318` が必要な場合があります
* **エンドポイントのパスが正しいか**：環境変数 `OTEL_EXPORTER_OTLP_LOGS_ENDPOINT` を使用する場合は、末尾に `/v1/logs` を含めた `http://localhost:4318/v1/logs` を指定してください
* **APIキーが正しいか**：Write 権限のある APIキーが設定されているか確認してください
* **サービス名が設定されているか**：`service.name` リソース属性が空になっていないか確認してください

## サードパーティアプリケーションのログを送信する

自社開発のアプリケーション以外にも、Nginx などのプロキシやロードバランサー、データベースといった**サードパーティアプリケーション**のログをコレクター経由で Mackerel に送信できます。アプリケーションのログ出力方式に合わせて、コレクターの**レシーバー**を選択します。

ここでは、代表的な2つのパターンを紹介します。

### Nginx のアクセスログを Syslog Receiver で送信する

Nginx の `access_log` ディレクティブで syslog 出力先をコレクターに向け、コレクターの **Syslog Receiver** で受信する構成です。

##### Nginx の設定

`nginx.conf` の `http` ブロックで、アクセスログの出力先をコレクターの Syslog Receiver に変更します。

```nginx
http {
    access_log syslog:server=localhost:54526,tag=nginx_access combined;
}
```

##### コレクターの設定（config.yaml）

以下は、OpenTelemetry Collector Contrib で Nginx の syslog を受信し、Mackerel に送信する設定例です。

```yaml
receivers:
  syslog:
    udp:
      listen_address: "0.0.0.0:54526"
    protocol: rfc3164
    location: Asia/Tokyo

processors:
  resource:
    attributes:
      - key: service.name
        value: "nginx"
        action: insert

exporters:
  otlp_http/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": "${env:MACKEREL_APIKEY}"
    sending_queue:
      batch:
        max_size: 3500000
        sizer: bytes

service:
  pipelines:
    logs:
      receivers: [syslog]
      processors: [resource]
      exporters: [otlp_http/mackerel]
```

設定のポイントは以下のとおりです。

* **Syslog Receiver**：`rfc3164` プロトコルでポート 54526 を listen し、Nginx からの syslog メッセージを受信します
* **resource プロセッサー**：`service.name` 属性を `"nginx"` に設定します（`action` の `insert` は属性が存在しない場合のみ追加し、`upsert` は既存の値があっても上書きします）。この値が Mackerel のWebコンソールでのサービス選択に対応します

MDOT コレクターを利用する場合も、エクスポーターを `mackerel_otlp` に置き換えるだけで、それ以外は同じです。

<div class="note">
<p>Syslog Receiver を使う場合、Docker でコレクターを起動する際に syslog 受信用のポート（上記の例では 54526）もポートマッピングに追加してください。</p>
</div>

### データベースのログを Filelog Receiver で送信する

データベースがファイルに出力するログは、コレクターの **Filelog Receiver** で読み取り、Mackerel に送信できます。

##### コレクターの設定（config.yaml）

以下は、`2026-06-11T14:43:28.124878Z 0 [System] [MY-015015] [Server] MySQL Server - start.` のようなMySQLが出力するログを、OpenTelemetry Collector Contrib で読み取り、Mackerel に送信する設定例です。

```yaml
receivers:
  file_log:
    include:
      - /var/log/mysql/error.log
    start_at: end
    multiline:
      line_start_pattern: ^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}\.\d+Z
    operators:
      - type: regex_parser
        regex: '(?P<timestamp>[^ ]+).*$'
        timestamp:
          parse_from: attributes.timestamp
          layout_type: gotime
          layout: "2006-01-02T15:04:05.999999Z"

processors:
  resource:
    attributes:
      - key: service.name
        value: "mysql"
        action: insert
  transform:
    error_mode: ignore
    log_statements:
      - context: log
        statements:
          - set(severity_number, SEVERITY_NUMBER_INFO)  where attributes["level"] == "Note" or attributes["level"] == "System"
          - set(severity_number, SEVERITY_NUMBER_WARN)  where attributes["level"] == "Warning"
          - set(severity_number, SEVERITY_NUMBER_ERROR) where attributes["level"] == "Error"

exporters:
  otlp_http/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": "${env:MACKEREL_APIKEY}"
    sending_queue:
      batch:
        max_size: 3500000
        sizer: bytes

service:
  pipelines:
    logs:
      receivers: [file_log]
      processors: [resource, transform]
      exporters: [otlp_http/mackerel]
```

設定のポイントは以下のとおりです。

* **Filelog Receiver**
  * `include` で読み取るログファイルのパスを指定します。
  * `start_at: end` を設定すると、コレクター起動後に追記された行のみを読み取ります。
  * `multiline` で新しいログになる箇所を指定することで、複数行のログをひとまとめにできます。
  * `regex_parser` で正規表現を利用してログに記載された時刻とログレベルをパースします。
* **resource プロセッサー**：`service.name` 属性を `"mysql"` に設定します。この値が Mackerel のWebコンソールでのサービス選択に対応します
* **transform プロセッサー** : Filelog Receiverを利用してパースした level を、Mackerelが重要度判定に利用する severity_number に変換します。この例では以下のように変換します。

|ログレベル|severity_number|
|-|-|
|Error|SEVERITY_NUMBER_ERROR(17)|
|Warning|SEVERITY_NUMBER_WARN(13)|
|InfoまたはSystem|SEVERITY_NUMBER_INFO(9)|

MDOT コレクターを利用する場合も、エクスポーターを `mackerel_otlp` に置き換えるだけで、それ以外は同じです。

<div class="note">
<p>Filelog Receiver を使う場合、Docker でコレクターを起動する際にログファイルが配置されているディレクトリをボリュームマウントしてください。</p>
</div>

### JSONログを解析して送信する

アプリケーションがJSON形式でログを出力している場合、コレクターでJSONとして解析することで、ログの各フィールドを**属性**として扱えるようになります。属性として格納されたフィールドは検索のフィルター条件に使えるため、特定のユーザーIDやリクエストパス、エラーコードでの絞り込みが可能になります。

* [属性によるフィルタで構造化ログを絞り込む](https://mackerel.io/ja/docs/entry/log/search-and-analysis#属性によるフィルタで構造化ログを絞り込む)

また、メールアドレスや電話番号、認証トークンなどの機微情報を、送信前に加工することも可能になります。

* [機微情報の加工](https://mackerel.io/ja/docs/entry/log/filtering-sampling#機微情報の加工)

解析方法はレシーバーによって異なります。

##### Filelog Receiver の場合

以下は、`{"level":"info","message":"request received","path":"/api/users","status":200}` のようなJSON形式のログを Filelog Receiver で読み取る設定例です。

```yaml
receivers:
  file_log:
    include:
      - /var/log/my-sample-app/app.log
    start_at: end
    operators:
      - type: json_parser
        parse_to: body

processors:
  resource:
    attributes:
      - key: service.name
        value: "my-sample-app"
        action: insert

exporters:
  otlp_http/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": "${env:MACKEREL_APIKEY}"
    sending_queue:
      batch:
        max_size: 3500000
        sizer: bytes

service:
  pipelines:
    logs:
      receivers: [file_log]
      processors: [resource]
      exporters: [otlp_http/mackerel]
```

`json_parser` オペレーターのポイントは `parse_to: body` の指定です。`parse_to` を省略した場合、デフォルトでは解析結果が `attributes` に展開されます。`parse_to: body` を指定することで、解析結果がログの `body` フィールドに格納されます。

##### Fluent Forward Receiver の場合

Fluent Bit や Fluentd から **Fluent Forward プロトコル**でログを受信する場合は、**Fluent Forward Receiver** を使います。ただし、このレシーバーはログ本文を文字列のまま受け取るため、たとえアプリケーションからはJSON形式で送っていたとしても、`operators` によるJSON解析はできません。代わりに `transform` プロセッサーで解析します。

また、Fluent Forward 経由のログは既存のパイプラインとは独立した専用のパイプラインとして定義する必要があります。

```yaml
receivers:
  fluent_forward:
    endpoint: 0.0.0.0:8006

processors:
  resource/fluent_forward:
    attributes:
      - key: service.name
        value: "my-sample-app"
        action: insert
  transform/fluent_forward:
    error_mode: silent
    log_statements:
      - context: log
        statements:
          - set(body, ParseJSON(body)) where IsString(body)

exporters:
  otlp_http/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": "${env:MACKEREL_APIKEY}"
    sending_queue:
      batch:
        max_size: 3500000
        sizer: bytes

service:
  pipelines:
    logs/fluent_forward:
      receivers: [fluentf_orward]
      processors: [resource/fluent_forward, transform/fluent_forward]
      exporters: [otlp_http/mackerel]
```

設定のポイントは以下のとおりです。

* **transform/fluent_forward**：`body` が文字列の場合にのみJSONとして解析し、結果を `body` に上書きします。`error_mode: silent` を設定することで、JSON形式でないログが混在しても処理を継続します
* **パイプラインの分離**：`transform/fluent_forward` の処理を Fluent Forward 由来のログにのみ適用するため、専用のパイプライン `logs/fluent_forward` として分離します

MDOT コレクターを利用する場合も、エクスポーターを `mackerel_otlp` に置き換えるだけで、それ以外は同じです。

## 送信されるログの構造を理解する

Mackerel に届くログがどのような構造になるかを把握しておくと、ログの検索や分析に役立ちます。

### OTel ログのデータ構造

Mackerel に送信されるログは、以下のフィールドで構成されます。

| フィールド | 説明 | 例 |
|-----------|------|-----|
| タイムスタンプ | ログが出力された時刻 | `2026-06-01 10:30:15.123` |
| Severity Number | 重要度を表す数値（1〜24） | `9`（INFO） |
| サービス名 | `service.name` リソース属性の値 | `my-sample-app` |
| メッセージ | ログ本文 | `order processed` |

Severity Number は以下のように重要度にマッピングされます。

| Severity Number | 重要度 |
|----------------|--------|
| 1〜4 | TRACE |
| 5〜8 | DEBUG |
| 9〜12 | INFO |
| 13〜16 | WARN |
| 17〜20 | ERROR |
| 21〜24 | FATAL |

### ロガーの出力とフィールドの対応

slog でログを出力した場合、各要素は以下のように Mackerel 上のフィールドに対応します。

```go
slog.InfoContext(r.Context(), "request received")
```

| slog の出力 | Mackerel 上のフィールド |
|------------|---------------------|
| `"request received"` | メッセージ（msg） |
| `slog.LevelInfo` | Severity Number 9（INFO） |
| リソースの `service.name` | サービス名 |

## まとめと次のステップ

このヘルプでは、以下の手順でアプリケーションのログを Mackerel に送信する方法を解説しました。

1. ログ送信の全体像と方法の選択
2. コレクターの準備と起動
3. アプリケーションへの OTel ログブリッジの接続（Go + slog）
4. サードパーティアプリケーション（Nginx、データベース）のログ送信
5. JSONログの解析と送信
6. Mackerel のWebコンソールでの動作確認

ここからさらにログ機能を活用するために、以下のヘルプも参照してみてください。

* **「[ログの検索・分析](https://mackerel.io/ja/docs/entry/log/search-and-analysis)」ヘルプ**：属性によるフィルタやヒストグラムのドリルダウン、検索条件の保存と共有など、ログ画面の活用方法を解説しています
* **「[ログのデータ量とコストを見積もる](https://mackerel.io/ja/docs/entry/log/cost-estimation)」ヘルプ**：送信するログのデータ量を見積もり、コストを管理する方法を解説しています
