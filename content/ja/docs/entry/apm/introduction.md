---
Title: APM（Application Performance Monitoring）の基本とMackerel APMで得られる価値
Date: 2026-03-09T19:29:50+09:00
URL: https://mackerel.io/ja/docs/entry/apm/introduction
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901348023848
---

APM（Application Performance Monitoring）の基本的な概念と、Mackerel APMを導入することで得られる価値について説明します。

**目次**

[:contents]

## APM（Application Performance Monitoring）とは

APMは、アプリケーションのパフォーマンスを監視し、問題の発見と解決を支援する手法です。

APMを利用すると、次のようなことが可能になります。

* パフォーマンスのボトルネックを特定
* エラー発生時の迅速な原因究明
* ユーザー体験に影響する問題の早期発見

## APMが必要な理由

### インフラ監視だけでは不足しがちなこと

従来のインフラ監視では、サーバーのCPU使用率やメモリ使用量、ディスクI/Oなどのリソース状況を把握できます。しかし、アプリケーション内部で何が起きているかまでは分かりません。

例えば、次のような状況があります。

* ユーザーから「遅い」「エラーになった」という報告を受けても、インフラの監視だけでは実際のユーザー体験と結びつけて原因を特定しづらい
* ログにエラーが記録されていても、どのリクエストがどのような処理フローでエラーになったのか追跡するのは困難
* レスポンスが遅くなっているとき、それがデータベースの問題なのか、外部APIの問題なのか、アプリケーションコードの問題なのかを切り分けるのに時間がかかる
* 複数のマイクロサービスにまたがるリクエストで、どのサービスのどの処理がボトルネックになっているのか把握しづらい

### APMが解決すること

APMを導入すると、これらの問題を解決できます。

**エラーの原因調査**

エラーが発生したリクエストのトレース（処理の流れ）を確認することで、リクエスト中のどの処理でどのようなエラーが発生したのかを素早く特定できます。スタックトレースやエラーメッセージも合わせて記録されるため、原因究明が容易になります。

**パフォーマンス低下の分析**

各処理にかかった時間を可視化することで、どこがボトルネックになっているのか一目で分かります。データベースクエリが遅いのか、外部APIの呼び出しに時間がかかっているのか、あるいはアプリケーションコード自体に問題があるのかを判断できます。

**サービスをまたぐ流れの追跡**

マイクロサービスアーキテクチャでは、1つのリクエストが複数のサービスを経由します。APMを使うと、リクエストがどのサービスをどの順序で経由したか、各サービスでどれだけ時間がかかったかを追跡できます。

## Mackerel APMで得られる価値

### インフラのメトリックとアプリケーションの分析を一つのツールで

Mackerelは従来からサーバーやコンテナの監視（インフラ監視）を提供してきました。APMも併用することで、インフラとアプリケーションの両方を同じ監視ツールで一元的に把握できます。

1. APMでAPIのレスポンス遅延を検知
2. トレースを確認し、どの処理に時間がかかっているかを特定
3. トレースから原因を分析できない場合にホストのCPU使用率やメモリ使用率を確認
4. インフラが原因なのか、アプリケーションコードが原因なのかを切り分け

これにより、問題の切り分けにかかる時間を短縮できます。

### MackerelのAPMで実現できること

Mackerel APMでは、次のような統計や事実を観察できます。

* **トレースを元にした各種サマリー**：REDメソッドのメトリック（リクエスト数、エラー率、レイテンシー）を自動的に可視化
* **HTTPリクエストの統計情報**：エンドポイントごとのリクエスト数、レイテンシー（平均、最大、最小、P95など）、エラー率など
* **データベースクエリの統計情報**：実行回数、実行時間（平均、最大、最小、P95など）、遅いクエリの特定
* **トレースの詳細情報**：個々のリクエストがどのような処理を経て、どれくらい時間がかかったかの詳細な記録
* **エラーの詳細情報**：エラーが発生したトレースを自動的にグループ化し、課題として管理

<figure class="figure-image figure-image-fotolife" title="サマリー / HTTPリクエスト統計情報 / DBクエリ統計情報"><div class="images-row mceNonEditable">[f:id:mackerelio:20260210180354p:plain][f:id:mackerelio:20260210180443p:plain][f:id:mackerelio:20260210180501p:plain]</div><figcaption>サマリー / HTTPリクエスト統計情報 / DBクエリ統計情報</figcaption></figure>

### 業界標準の技術を採用

MackerelのAPMは、テレメトリーデータのためのフレームワークである[OpenTelemetry](https://opentelemetry.io/ja/)に対応しています。OpenTelemetryには以下の特徴があります。

* さまざまなベンダーやツールがサポートする統一的な標準規格
* トレース、メトリック、ログなど[主要なシグナル](https://opentelemetry.io/ja/docs/concepts/signals/)を統一的に扱える
* Go、Java、PHP、Ruby、Python、JavaScript、C#/.NETなど[主要な言語に対応](https://opentelemetry.io/ja/docs/languages/)
* 各言語の主要なフレームワークやライブラリ向けの自動計装が充実
  * Ruby on Rails、Spring Boot、Laravel、Flask など

MackerelがOpenTelemetryに対応していることで、以下のメリットがあります。

* **豊富なライブラリ**：OpenTelemetryコミュニティが提供する各種ライブラリやツールを活用できます
* **エコシステムの恩恵**：OpenTelemetryの進化に合わせて、新しい機能やサポート言語を利用できます
* **ベンダーロックインの回避**：将来的に他のオブザーバビリティツールに移行する場合も、計装コードをそのまま利用できます

## MackerelのAPMを利用するためには

### 計装をする

MackerelのAPMを利用するには、アプリケーションに[計装（instrumentation）](https://opentelemetry.io/ja/docs/concepts/instrumentation/)が必要です。計装とは、アプリケーションからトレースデータなどのテレメトリーシグナルを送出する作業のことです。

### 計装の手段

計装の方法には以下のようなものがあります。

* **ゼロコード計装**：アプリケーションコードを変更せずに、実行時の設定だけで計装できます
* **ライブラリ計装（自動計装）**：わずかな設定コードを追加するだけで、自動的にトレースを取得できます
* **手動計装**：より詳細な情報を取得したい場合に、必要な箇所にコードを追加します

まずはゼロコード計装から始めて、特定の処理の内容をより詳細に追いたくなった場合などに、自動計装や手動計装を追加していくことをお勧めします。

### Mackerelには導入サポートの体制があります

Mackerel APMの導入に際してつまずいた場合も、Mackerelのサポートチームがお手伝いします。技術的な質問や導入に関する相談はお気軽に[お問い合わせ](https://support.mackerel.io/hc/ja/requests/new)ください。

## 次のステップ

APMの基本と価値について理解できたら、次は仕組みを学びましょう。

* [APMの仕組みを理解する](https://mackerel.io/ja/docs/entry/apm/concepts) - トレース、スパン、データフローなどの基本概念

<!--
* [APM導入のための環境構成](/ja/docs/entry/apm/architecture) - 具体的な計装手法の選択と環境構成
-->

すぐに試したい方は、言語別のガイドを参照してください。

* [GoにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/go)
* [Node.jsにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/nodejs)
* [PHPにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/php)
* [PythonにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/python)
* [RubyにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/ruby)
* [.NETにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/dotnet)
* [その他の言語にOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/other-language)
