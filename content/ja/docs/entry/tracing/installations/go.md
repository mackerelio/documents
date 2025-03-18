---
Title: トレース - GoにOpenTelemetryを導入する
Date: 2025-03-13T16:34:45+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/go
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960398
---

MackerelはOpenTelemetryの仕組み (計装)を利用してデータを取得しています。

このページではGoのデータをMackerelに送信する方法を解説します。

[:contents]

## Go向けOpenTelemetry

OpenTelemetry には Go用のSDKが用意されています。

このSDKに加えて、GinやGorm用のSDKを使用すると、色々な範囲を計装することができます。

[https://opentelemetry.io/ja/docs/languages/go/instrumentation/:embed:cite]

### Collectorを使用するべきか

データをMackerelに送信する際に、SDKから直接送信するだけではなく、Collectorを使うこともできます。

Collectorを使うかどうかを決める際は以下のページを参考にしてください。

[Collectorを使うかどうかの判断について](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## 導入方法

Goには複数のWebフレームワークが存在しますが、このページでは最も基本的な `net/http`への導入方法を説明します。 他のフレームワークを使っている場合も以下とほぼ同じ方法で計装することができます。

以下のステップでMackerelトレーシング機能を導入できます。

1. go get
2. 初期設定
3. ミドルウェアの挿入
4. エラーを補足
5. 独自の計装の追加 (任意)

### 1. go get

以下のモジュールをインストールします。

```sh
go get go.opentelemetry.io/otel \
	go.opentelemetry.io/otel/trace \
	go.opentelemetry.io/otel/exporters/otlp/otlptrace
```

このコマンドでは基本的なモジュールのみインストールしています。

しかし、OpenTelemetryにはGinやGorm、AWSなど色々な用途似対応したモジュールがあり、アプリケーションに合わせて追加することでより詳細に計装することが出来るようになります。

各モジュールは、OpenTelemetryのページから見つけることができます。

[https://opentelemetry.io/ecosystem/registry/?language=go&component=instrumentation:embed:cite]

### 2. 初期設定

OpenTelemetryのデータをMackerelに送信するためには、TracerProviderに以下の項目を設定する必要があります。

* Exporter
* Resource

例えば、以下のようにすると、SDKから直接Mackerelへ送信することができます。

```go
import (
  ...
  "go.opentelemetry.io/otel/exporters/otlp/otlptrace"
  "go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp"
  "go.opentelemetry.io/otel/exporters/stdout/stdouttrace"
  "go.opentelemetry.io/otel/sdk/trace"
  "go.opentelemetry.io/otel/attribute"
  semconv "go.opentelemetry.io/otel/semconv/v1.20.0"
)

func initTracerProvider(ctx context.Context) (func(context.Context) error, error) {
  client := otlptracehttp.NewClient(
    otlptracehttp.WithEndpoint("otlp-vaxila.mackerelio.com"),
    otlptracehttp.WithHeaders(map[string]string{
      "Accept":         "*/*",
      "Mackerel-Api-Key": os.Getenv("MACKEREL_API_KEY"),
    }),
    otlptracehttp.WithCompression(otlptracehttp.GzipCompression),
  )
  exporter, err := otlptrace.New(ctx, client)
  // デバッグ時にはstdouttraceが便利
  // exporter, err := stdouttrace.New()
  if err != nil {
    return nil, err
  }
  
  resources, err := resource.New(
    ctx,
    resource.WithProcessPID(),
    resource.WithHost(),
    resource.WithAttributes(
      semconv.ServiceName("acme_service"),
      semconv.ServiceVersion("vX.Y.Z"),
      semconv.DeploymentEnvironment("production"),
    ),
  )
  if err != nil {
    return nil, err
  }
  
  tp := trace.NewTracerProvider(
    trace.WithBatcher(exporter),
    trace.WithResource(resources),
  )
  otel.SetTracerProvider(tp)
  otel.SetTextMapPropagator(propagation.TraceContext{})

  return tp.Shutdown, nil
}
```

この例では次の項目を設定しています。

* Exporter
  * Endpoint & Path
    * Endpoint を `otlp-vaxila.mackerelio.com` と設定することで、データをMackerelに送信するようになります。
    * Collectorを利用する場合は、Collectorのエンドポイント(localhost:4318 など) を設定してください
  * Headers
    * `Mackerel-Api-Key` と `Accept` のヘッダーを設定することでMackerelと通信することができます。
    * `Mackerel-Api-Key` には、Mackerelで発行された書き込み権限のあるAPIキーを設定してください。APIキーの権限を変更した際は反映まで1分ほどお待ちください。
    * Collectorを利用する場合、ヘッダーは必要ないでしょう。
  * Compression
    * データを送信する時にデータの圧縮方法を指定することができます。指定しなかった場合は圧縮されません。
* Resource
  * TracerProviderのResourceを設定することで、データがどこから来たかわかるようになります。

### 3. ミドルウェアの挿入

HTTPサーバーを建てている場合、各リクエストの情報を収集するために専用のミドルウェアを挿入する必要があります。

`net/http` を使用している場合には `otelhttp` を利用できます。

例えば、前節で作った `initTracerProvider` 関数を使用すると、以下のようにできます。

```go
import (
  ...
  "go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp"
)

func main() {
  ctx := context.Background()
  shutdown, err := initTracerProvider(ctx)
  if err != nil {
    panic(err)
  }
  defer shutdown(ctx)

  otelHandler := otelhttp.NewHandler(
    http.HandlerFunc(awesomeActionHandler),
    "awesome_span_name",
  )
  http.Handle("/awesome_path/", otelHandler)
  http.ListenAndServe(":80", nil)
}

func awesomeActionHandler(w http.ResponseWriter, r *http.Request) {
  ...
}
```

#### ⚠️ 他のフレームワークを使用している場合

このページでは `net/http` への計装方法を解説していますが、GinやEchoなどを使用する場合であっても、類似したインターフェースを持っているため、基本的に `net/http` と変わりません。

つまり、以下のステップで計装できます。

1. go get でフレームワークに対応したモジュールを取得する
2. 初期設定をする
3. ミドルウェアを挿入してトレースを開始する

また、ルーティング周りが整備されている関係上、一般的に `net/http` よりも計装が簡単です。

各フレームワークに対応したモジュールは以下のページから検索できます。

[https://opentelemetry.io/ecosystem/registry/?language=go&component=instrumentation:embed:cite]

### 4. エラーを補足

Goの言語制約上、エラーを自動で補足することはできないため、エラーが発生した場合にエラーをSpanに追加するコードを書く必要があります。



エラーの追加には `Span.RecordError()` 関数を使用します。 また、`WithStackTrace()` を使うとスタックトレースを記録することもできます。

例えば、前節で作成したハンドラー (`awesomeActionHandler`) で発生したエラーを補足するには以下のようにします。

```go
import (
  ...
  "go.opentelemetry.io/otel/trace"
  semconv "go.opentelemetry.io/otel/semconv/v1.17.0"
)

func awesomeActionHandler(w http.ResponseWriter, r *http.Request) {
  err := someAction()
  if err != nil {
    recordError(r.Context(), err)
    w.WriteHeader(500)
    return
  }

  ...
}

func recordError(ctx context.Context, err error) {
  span := trace.SpanFromContext(ctx)
  span.RecordError(err, trace.WithStackTrace(true))
}
```

### 5. 独自の計装の追加 (任意)

独自のSpanを追加することで、任意の範囲を計装することができます。

計装することで、変数の値や処理時間を記録することができます。

具体的には、下のように `Tarcer.Start()` と `Span.End()` で囲むと計装を追加できます。

```go
const (
  instrumentationName    = "my-instrumentron"
  instrumentationVersion = "v0.1.0"
)

var (
  tracer = otel.GetTracerProvider().Tracer(
  instrumentationName,
    trace.WithInstrumentationVersion(instrumentationVersion),
    trace.WithSchemaURL(semconv.SchemaURL),
  )
)

func awesomeActionHandler(w http.ResponseWriter, r *http.Request) {
  ctx, span := tracer.Start(r.Context(), "awesome_action")
  defer span.End()

  ...
}
```

計装の方法は他にも用意されています。詳細はOpenTelemetryのドキュメントを参照してください。

[https://opentelemetry.io/ja/docs/languages/go/instrumentation/:embed:cite]
