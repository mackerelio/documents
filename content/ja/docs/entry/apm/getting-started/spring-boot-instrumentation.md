---
Title: Mackerel APM導入ガイド ― Spring Boot における OpenTelemetry 計装
Date: 2026-09-11T11:02:12+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/spring-boot-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293560
---

Spring Bootアプリケーションでは、**OpenTelemetry Spring Boot Starter**を依存関係に追加するだけでトレースの収集を始められます。Java Agentのようなバイトコード操作を行わないため、GraalVM Native Imageとの互換性があり、Spring Boot の Auto-Configuration の仕組みに沿った形で計装が統合されます。

この章では、Spring Boot StarterによるOpenTelemetry計装のセットアップ手順を解説します。Spring Bootアプリケーション以外のJavaアプリケーションには[Java Agent によるゼロコード計装](./java-agent-instrumentation)を利用してください。

[:contents]

## 前提条件

* Spring Boot 4.0以降のアプリケーション
* Maven または Gradle によるビルド環境
* Mackerel OpenTelemetryコレクターがセットアップ済みであること（[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「Mackerel OpenTelemetryコレクターの準備」を参照）

## セットアップ手順

### 1. 依存関係の追加

**Maven の場合：**

`pom.xml` にStarterを追加します。

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-opentelemetry</artifactId>
    </dependency>
</dependencies>
```

**Gradle の場合：**

```gradle
implementation "org.springframework.boot:spring-boot-starter-opentelemetry"
```

### Spring Initializrを使う場合

[Spring Initializr](https://start.spring.io/)で新しいプロジェクトを作成する際に、Dependencies から「OpenTelemetry」を選択すると、必要な依存関係が自動的に追加されます。

### 2. 設定ファイルの編集

`application.yml`（または `application.properties`）にサービス名とエクスポート先を設定します。

```yaml
spring:
  application:
    name: my-spring-app
management:
  opentelemetry:
    tracing:
      export:
        otlp:
          endpoint: http://localhost:4318/v1/traces
  tracing:
    sampling:
      probability: 1.0
```

`endpoint` にはMackerel OpenTelemetryコレクターのHTTPポート（4318）を指定します。パスに `/v1/traces` が必要な点に注意してください。

検証段階では `probability: 1.0`（全リクエストをトレース）に設定してください。本番環境でのサンプリング設定については[フィルタリングとサンプリングによるデータ量・コストの最適化](./filtering-and-sampling)を参照してください。

### 3. ビルドと起動

```bash
# Maven
mvn spring-boot:run

# Gradle
./gradlew bootRun
```

アプリケーションコードの変更は不要です。

### 4. 動作確認

アプリケーションにリクエストを送信し、Mackerel APMの画面でトレースが表示されることを確認します。確認方法は[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「最初のトレースを確認してみよう」を参照してください。

## 自動計装の対象

Spring Boot Starterは、以下のライブラリを自動的に計装します。

* **Spring Web**：Spring MVC、Spring WebFlux のリクエスト処理
* **HTTPクライアント**：RestClient、RestTemplate、WebClient
* **データアクセス**：JDBC、R2DBC、MongoDB
* **メッセージング**：Kafka、RabbitMQ
* **ロギング**：Logback（トレースIDの自動付与）

Spring Boot Starterの計装はMicrometerのObservation APIを通じて動作するため、個別のライブラリ単位での無効化はJava Agentほど細かくは制御できません。計装をライブラリ単位で制御したい場合は、[Java Agent によるゼロコード計装](./java-agent-instrumentation)の利用を検討してください。

## よくある問題と対処法

### トレースが表示されない

* `management.opentelemetry.tracing.export.otlp.endpoint` がコレクターのHTTPポート（4318）を指しているか確認してください。gRPCポート（4317）ではありません
* エンドポイントのパスに `/v1/traces` が含まれているか確認してください
* `management.tracing.sampling.probability` が `0` になっていないか確認してください

## 次のステップ

* **Java Agentとの比較を知りたい** → [Java Agent によるゼロコード計装](./java-agent-instrumentation)で、Agent方式の詳細を紹介しています
* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/java-agent-instrumentation" rel="prev">Java Agent によるゼロコード計装</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/python-zero-code-instrumentation" rel="next">Python におけるゼロコード計装</a></li>
</ul>
</nav>
