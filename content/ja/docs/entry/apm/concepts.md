---
Title: Mackerel APMの基本概念 ― トレース、スパン、データフロー
Date: 2026-03-09T19:49:36+09:00
URL: https://mackerel.io/ja/docs/entry/apm/concepts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901348087757
---

Mackerel APMを支える基本概念について説明します。OpenTelemetryの概要、トレースやスパンといった分散トレーシングの仕組み、トレースデータがMackerelに届くまでのデータフローを扱います。

**目次**

[:contents]

## OpenTelemetryとは

OpenTelemetryは、トレースやメトリックなどのテレメトリーデータを収集・エクスポートするためのフレームワークです。言語やベンダーに依存しない統一的なAPIを提供します。Mackerel APMはOpenTelemetry形式のトレースデータを受け付けるため、OpenTelemetryの計装ライブラリをそのまま利用できます。OpenTelemetry対応のメリットについては、[APMの基本とMackerel APMで得られる価値](https://mackerel.io/ja/docs/entry/apm/introduction#業界標準の技術を採用)を参照してください。

## 分散トレーシングとは

分散トレーシングとは、複数のサービスやコンポーネントにまたがるリクエストの処理の流れを「トレース」と「スパン」という単位で追跡する手法です。Mackerel APMは、収集したトレースデータを可視化し、パフォーマンスの傾向を自動的に集計します。

### スパンとは

スパンは、1つの処理単位を表します。例えば以下のような処理がそれぞれ1つのスパンになります。

* HTTPリクエストの受信処理
* データベースへのクエリ実行
* 外部APIの呼び出し
* キャッシュへのアクセス

各スパンには以下のような情報が記録されます。

| 項目 | 説明 | 例 |
|------|------|-----|
| 処理名 | スパンを識別する名前 | `GET /api/users`、`SELECT * FROMusers` |
| 開始時刻・終了時刻 | 処理にかかった時間 | 開始: 2025-01-15T12:00:00.000Z、終了: 2025-01-15T12:00:03.150Z |
| ステータス | 処理の結果 | OK、ERROR、UNSET |
| 属性 | 追加のメタデータ | HTTPステータスコード、データベース名など |

このほかにも、スパンの種類（SERVER、CLIENTなど）、イベント（例外情報のログなど）、リソース情報（ホスト名、デプロイ環境など）が記録されます。

### トレースとは

トレースは、1つのリクエストやトランザクション全体の流れを構成する複数のスパンをまとめたものです。

例えば、ユーザーからのAPIリクエストを処理する際、以下のようなスパンが生成されます。

1. HTTPリクエストを受信（スパン1）
2. データベースからユーザー情報を取得（スパン2）
3. キャッシュから設定を取得（スパン3）
4. 外部APIに通知を送信（スパン4）
5. HTTPレスポンスを返却（スパン1の終了）

これらのスパンが1つのトレースとしてまとめられ、リクエスト全体の処理の流れを追跡できます。

[f:id:mackerelio:20260217201951p:plain]

### トレースの可視化

スパンには親子関係があります。上の例では、スパン1（HTTPリクエスト）が親スパンとなり、スパン2〜4はその子スパンになります。この親子関係により、どの処理がどの処理の中で実行されたかが分かります。

Mackerel APMの画面では、トレースを可視化することによって、どの処理がどのくらい時間を要しているか、どこでエラーが発生しているかを直感的に把握できます。

* 横軸: 時間の流れ（左から右へ）
* 縦軸: スパンの階層構造（親子関係）
* スパンの長さ: 各処理の所要時間
* スパンの色: 複数のサービスをまたぐトレースの可視化

[f:id:mackerelio:20260126222124p:plain]

## トレースのデータフロー

アプリケーションで計装されたトレースデータをMackerelに送信する方法は2つあります。OpenTelemetry Collectorを経由する方法と、アプリケーションから直接送信する方法です。

[f:id:mackerelio:20260217202023p:plain]

### OpenTelemetry Collectorとは

[OpenTelemetry Collector](https://opentelemetry.io/ja/docs/collector/)は、テレメトリーデータの受信、処理、エクスポートを行うコンポーネントです。mackerel-agentがホストのメトリックを収集・送信するのに対し、OpenTelemetry Collectorはアプリケーションなどから受信したトレースデータを中継・加工する役割を担います。アプリケーションとバックエンド（Mackerel）の間に配置することで、バッチ処理や再試行、サンプリング、フィルタリングなどの処理をアプリケーションから切り離せます。

### 送信方法の比較

| 方法 | メリット | デメリット |
|------|----------|------------|
| Collector経由 | バッファリングによる信頼性向上、サンプリングやフィルタリングが可能、複数アプリケーションから送信されたスパンの集約 | Collectorの運用が必要 |
| 直接送信 | 構成がシンプル、運用コンポーネントが少ない | アプリケーション障害時にデータ欠損の恐れ、柔軟な加工が難しい |

本番環境ではCollector経由を推奨しますが、開発環境や小規模な構成では直接送信でも問題ありません。<!--詳細は[APM導入のための環境構成](https://mackerel.io/ja/docs/entry/apm/architecture)で説明しています。-->

## 次のステップ

<!--
トレース、スパンの概念やデータフローを理解できたら、次は導入のための環境構成を学びましょう。

* [APM導入のための環境構成](https://mackerel.io/ja/docs/entry/apm/architecture) - 計装手法の選択と環境構成

-->

すぐに試したい方は、言語別のガイドを参照してください。

* [GoにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/go)
* [Node.jsにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/nodejs)
* [PHPにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/php)
* [PythonにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/python)
* [RubyにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/ruby)
* [.NETにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/dotnet)
* [その他の言語にOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/other-language)
