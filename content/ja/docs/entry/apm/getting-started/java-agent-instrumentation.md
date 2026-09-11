---
Title: Mackerel APM導入ガイド ― Java Agent によるゼロコード計装
Date: 2026-09-11T11:02:08+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/java-agent-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293525
---

OpenTelemetry Java Agentは、JVMの `-javaagent` オプションを使ってアプリケーションコードを一切変更せずにトレースを収集できるゼロコード計装の方法です。Spring Framework、JDBC、Servlet、gRPCなど150以上のライブラリに対応しており、Javaアプリケーションの計装としては最も手軽に始められます。

この章では、Java Agentのセットアップ手順を詳しく解説します。Spring Bootアプリケーションでは[Spring BootにおけるOpenTelemetry 計装](./spring-boot-instrumentation)で紹介しているStarter方式も利用できますので、用途に応じて選択してください。

[:contents]

## 前提条件

* Java 8以降のJDKがインストールされていること
* Mackerel OpenTelemetryコレクターがセットアップ済みであること（[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「Mackerel OpenTelemetryコレクターの準備」を参照）

## セットアップ手順

### 1. Java Agentのダウンロード

OpenTelemetry Java AgentのJARファイルをダウンロードします。

```bash
curl -OL https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar
```

ダウンロードしたJARファイルは、アプリケーションからアクセスできる場所に配置してください。

### 2. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-java-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
```


| 環境変数 | 説明 | 設定例 |
|---------|------|--------|
| `OTEL_SERVICE_NAME` | Mackerel APM上での識別名 | `my-java-app` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | コレクターの受信アドレス | `http://localhost:4318` |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | 送信プロトコル | `http/protobuf` |


### 3. アプリケーションの起動

通常の起動コマンドに `-javaagent` オプションを追加します。

```bash
java -javaagent:/path/to/opentelemetry-javaagent.jar -jar app.jar
```

`-javaagent` オプションは `-jar` より前に指定する必要があります。Agentがクラスロード時にバイトコードを書き換えて計装を挿入するため、アプリケーションクラスより先にロードされる必要があるためです。

`JAVA_TOOL_OPTIONS` 環境変数を使うと、起動コマンドを変更せずにAgentを適用することもできます。

```bash
export JAVA_TOOL_OPTIONS="-javaagent:/path/to/opentelemetry-javaagent.jar"
java -jar app.jar
```

##### サーブレットコンテナ（Tomcat など）の場合

Tomcatのようなサーブレットコンテナでは、`-javaagent` オプションを直接指定するのではなく、`CATALINA_OPTS` 環境変数を使って設定します。Tomcatの `bin/setenv.sh`（Windowsでは `setenv.bat`）に以下のように記述してください。

```bash
export CATALINA_OPTS="$CATALINA_OPTS -javaagent:/path/to/opentelemetry-javaagent.jar"
export OTEL_SERVICE_NAME="my-tomcat-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
```

設定後、Tomcatを再起動するとAgentが適用されます。

### 4. 動作確認

アプリケーションにリクエストを送信し、Mackerel APMの画面でトレースが表示されることを確認します。確認方法は[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「最初のトレースを確認してみよう」を参照してください。

## 自動計装の対象

Java Agentは、以下のようなライブラリ・フレームワークを自動的に計装します。

* **Webフレームワーク**：Spring Web MVC、Spring WebFlux、JAX-RS、Servlet
* **データアクセス**：JDBC、Hibernate、MyBatis、R2DBC
* **HTTPクライアント**：OkHttp、Apache HttpClient、RestTemplate、WebClient
* **メッセージング**：Kafka、RabbitMQ、JMS
* **ロギング**：Logback、Log4j2（トレースIDの自動付与）
* **アプリケーションサーバー**：Tomcat、Jetty、Undertow

対応ライブラリの完全な一覧は、OpenTelemetry Java Instrumentationのリポジトリで確認できます。

Java Agentでは、環境変数 `OTEL_INSTRUMENTATION_[NAME]_ENABLED=false` を使って、特定のライブラリの計装を個別に無効化できます。不要なライブラリの計装を無効にすることでスパン数を削減できるだけでなく、バイトコード書き換え対象が減るため起動時間やランタイムのパフォーマンスも改善します。

## Java AgentとSpring Boot Starterの使い分け


| 観点 | Java Agent | Spring Boot Starter |
|------|-----------|-------------------|
| コード変更 | 不要 | 依存関係の追加が必要 |
| 対応アプリケーション | すべてのJavaアプリ | Spring Bootアプリのみ |
| 計装の仕組み | バイトコード書き換え | Spring Auto-Configuration |
| GraalVM Native Image | 非対応 | 対応 |
| 起動時のオーバーヘッド | やや増加 | 小さい |
| 依存管理 | JARファイルを別途管理 | Maven/Gradleで一元管理 |


Spring Bootアプリケーションであればどちらの方式も利用できます。「まずは手軽に試したい」場合はJava Agent、「ビルドプロセスに組み込んで管理したい」場合はSpring Boot Starterが適しています。

両方を同時に使用することは推奨されません。

## よくある問題と対処法

### トレースが表示されない

* `-javaagent` オプションが `-jar` より前に記述されているか確認してください
* コレクターが起動しているか、`OTEL_EXPORTER_OTLP_ENDPOINT` が正しいか確認してください

### 起動が遅くなった

Java Agentは起動時にバイトコードの書き換えを行うため、初回起動時に数秒程度の遅延が発生することがあります。アプリケーションの起動後は通常のパフォーマンスで動作します。

### 特定のライブラリが計装されない

自動計装の対象は、Java Agentがサポートしているライブラリのバージョンに依存します。対応バージョンはOpenTelemetry Java Instrumentationのリポジトリで確認できます。

## 次のステップ

* **Spring Bootアプリケーションの場合** → [Spring BootにおけるOpenTelemetry 計装](./spring-boot-instrumentation)で、Starter方式のセットアップを紹介しています
* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/php-zero-code-instrumentation" rel="prev">PHPにおけるゼロコード計装</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/spring-boot-instrumentation" rel="next">Spring Boot における OpenTelemetry 計装</a></li>
</ul>
</nav>
