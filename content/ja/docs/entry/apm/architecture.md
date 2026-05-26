---
Title: APMの計装手法とデータ収集の仕組み
Date: 2026-05-26T12:10:29+09:00
URL: https://mackerel.io/ja/docs/entry/apm/architecture
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901355864299
---

[APMの基本概念](https://mackerel.io/ja/docs/entry/apm/concepts)で説明したトレースをMackerelで可視化するには、アプリケーションに**計装（Instrumentation）** と呼ばれる仕組みを組み込む必要があります。計装の代表的な手法と実行環境ごとの考慮点、そしてテレメトリーデータの中継を担うOpenTelemetry Collectorの役割や配置パターンについて説明します。

**目次**

[:contents]

## 計装（Instrumentation）とは

[計装](https://opentelemetry.io/ja/docs/concepts/instrumentation/)とは、アプリケーションにコードや設定を追加し、トレースなどのテレメトリーデータを送出する作業のことです。[APMの基本概念](https://mackerel.io/ja/docs/entry/apm/concepts)で説明したトレースやスパンは、計装によって送出されます。

## 計装手法の種類

OpenTelemetryでは、計装の手法がコード変更の不要な**ゼロコード計装**と、計装ライブラリを使う**ライブラリ計装**の2種類に大きく分かれます。さらに、任意の箇所にスパン生成コードを追加する**コードベース計装**もあります。

### ゼロコード計装

ゼロコード計装は、アプリケーションコードを変更せずに計装する手法です。環境変数の設定や起動オプションの追加だけで、フレームワークやライブラリの処理を自動的にトレースできます。

言語やランタイムの特性に応じて、以下のような仕組みで実現されています。

* **ランタイム計装**：Java（Javaエージェント）のように、ランタイムが提供するフックを利用してバイトコードを動的に変更する
* **モンキーパッチ**：Python（`opentelemetry-instrument`）やNode.js（`--require` フラグ）のように、ライブラリの関数を実行時に差し替える
* **拡張モジュール**：PHP（拡張モジュール）のように、ランタイムの拡張機構を利用して計装コードを注入する

**メリット**

* アプリケーションコードの変更が不要
* 導入が非常に手軽で、すぐに効果を確認できる
* 対応フレームワークやライブラリであれば、幅広い処理を自動的にカバー

**デメリット**

* 対応していない言語やランタイムでは利用できない
* カスタマイズの自由度が低く、取得できる情報は対応ライブラリに依存する
* ランタイムの仕組みに依存するため、バージョンアップ時に互換性の問題が生じることがある

対応言語や各言語での具体的な導入方法については、[OpenTelemetryの公式ドキュメント](https://opentelemetry.io/ja/docs/zero-code/)をご覧ください。

### ライブラリ計装

ライブラリ計装は、OpenTelemetry SDKと計装ライブラリをアプリケーションに追加し、わずかな初期設定コードで自動的にトレースを取得する手法です。

例えば、Rubyの場合は`opentelemetry-instrumentation-rails`などのGemを追加し、初期化コードで`use_all`を呼び出すだけで、Ruby on Railsへのリクエストやデータベースクエリが自動的に計装されます。

**メリット**

* ランタイムの特性に依存しないため、ゼロコード計装より幅広い言語で利用できる
* 計装ライブラリの対応範囲内で、幅広い処理を自動的にカバー
* 初期設定コードの記述が必要だが、計装対象の個々の処理にコードを追加する必要はない
* テレメトリーデータの加工や送信先の切り替えなど、ゼロコード計装より柔軟なカスタマイズが可能

**デメリット**

* 初期設定のためのコード追加が必要
* 取得できる情報は計装ライブラリの対応範囲に限られる

### コードベース計装

コードベース計装は、コードの任意の箇所にスパン生成コードを追加する手法です。ゼロコード計装やライブラリ計装では取得できないビジネスロジック固有の情報（注文ID、処理した金額、ユーザーの操作内容など）を記録したい場合に使います。

コードベース計装は、ゼロコード計装やライブラリ計装と組み合わせて使うことが一般的です。フレームワークやライブラリの処理はゼロコード計装やライブラリ計装でカバーし、アプリケーション固有の処理はコードベース計装で補完します。

**メリット**

* あらゆる処理を自由に計装できる
* ビジネスロジック固有の属性やイベントを記録可能

**デメリット**

* 計装箇所ごとにコードの追加が必要
* 計装コードの管理・保守のコストがかかる

## 推奨される導入ステップと計装手法の比較

ここまで紹介した3つの計装手法は、どれか1つを選ぶというより、組み合わせて段階的に導入していくのがおすすめです。

1. **ゼロコード計装から始める**：対応言語を使っている場合は、まずゼロコード計装から始めます。コード変更なしでトレースの取得を始められ、そのまま本番環境でも運用できます。対応していない言語の場合はステップ2に進んでください。
2. **ライブラリ計装でカスタマイズする**：より柔軟な設定やカスタマイズが必要になったら、ライブラリ計装に移行または併用します。初期設定コードの追加が必要ですが、利用する計装ライブラリの取捨選択やSDKの挙動をコードで細かく制御できるようになります。
3. **必要に応じてコードベース計装で深掘りする**：ビジネスロジック固有の情報を記録したくなったら、コードベース計装を追加します。ゼロコード計装やライブラリ計装と組み合わせて、アプリケーション独自のスパンや属性を残しておくことで、想定外の問題が起きたときにも詳細な調査ができるようになり、システムのオブザーバビリティが高まります。

| | ゼロコード計装 | ライブラリ計装 | コードベース計装 |
|------|--------------|--------------|---------|
| コード変更 | 不要 | 初期設定のみ | 計装箇所ごとに必要 |
| 導入の手軽さ | 非常に手軽 | 手軽 | 箇所ごとに作業が必要 |
| カバー範囲 | 対応フレームワーク/ライブラリ | 計装ライブラリの対応範囲 | 自由にカスタマイズ可能 |
| カスタマイズ性 | 低い | 中程度 | 高い |
| 推奨される用途 | 手軽に導入したい場合 | カスタマイズが必要な場合 | ビジネスロジックの詳細な可視化 |

## 環境別の計装の考慮点

アプリケーションの実行環境によって、計装の導入方法が異なります。ここでは代表的な環境ごとの考慮点を紹介します。

**VM / 物理サーバー**

すでに稼働しているアプリケーションに対して、起動オプションや環境変数の変更、依存ライブラリや拡張モジュールの追加といった方法で計装を導入します。

**コンテナ（Docker / Kubernetes）**

計装ライブラリやエージェントをコンテナイメージに含めてビルドします。Kubernetesを利用している場合は、[OpenTelemetry Operator](https://opentelemetry.io/ja/docs/platforms/kubernetes/operator/)を使うことで、コンテナイメージを変更せずにゼロコード計装を自動注入できます。

**サーバーレス（AWS Lambda）**

AWS Lambdaでは、[OTel Collector Lambdaレイヤー](https://opentelemetry.io/ja/docs/platforms/faas/lambda-auto-instrument/)を利用してゼロコード計装を追加できます。

## OpenTelemetry Collectorの役割

OpenTelemetry Collectorの概要については[APMの基本概念](https://mackerel.io/ja/docs/entry/apm/concepts#OpenTelemetry-Collector%E3%81%A8%E3%81%AF)で紹介しています。ここでは、Collectorが担う代表的な処理を説明します。

### バッファリングとリトライ

アプリケーションから受信したスパンを一定量ためてからバックエンドにまとめて送信します。バックエンドが一時的に利用できない場合も、Collectorがデータを保持してリトライするため、アプリケーション側でのデータ欠損を防げます。

### フィルタリング

不要なスパンをバックエンドへ送信する前に除外できます。たとえば、ヘルスチェックエンドポイントへのリクエストやKubernetesのReadiness Probeなど、監視上のノイズとなるスパンを除外するといった用途があります。

### サンプリング

トラフィックが多い環境では、すべてのトレースを送信するとコストやネットワークの負荷が大きくなります。サンプリングにより、トレースの一部だけをバックエンドに送信できます。Collector上でサンプリングを行うことで、アプリケーションコードを変更せずにサンプリング戦略を一元管理できます。

## OpenTelemetry Collectorの配置パターン

ここでは、Collectorを「どこに配置するか」という観点で4つのパターンを紹介します。

### エージェント（同一ホスト）

アプリケーションと同一のホスト上でCollectorを動作させるパターンです。

[f:id:mackerelio:20260522184011p:plain]

Collectorをシステムサービス（systemd等）としてインストールし、アプリケーションからlocalhostで通信します。VMや物理サーバー環境に適しています。構成がシンプルで導入しやすいため、最初の選択肢として推奨されます。

### サイドカー

アプリケーションと同一の実行単位（KubernetesのPod、Amazon ECSのタスクなど）にCollectorを並行して配置するパターンです。

[f:id:mackerelio:20260526102959p:plain]

各アプリケーションインスタンスに専用のCollectorが付随するため、アプリケーションごとに独立した設定が可能です。コンテナ環境（Kubernetes、Amazon ECSなど）に適しています。

### デーモンセット

ノード（ホスト）ごとに1つのCollectorを配置するパターンです。

[f:id:mackerelio:20260522184018p:plain]

同一ノード上の複数のアプリケーションが1つのCollectorを共有します。サイドカーと比べてCollectorのリソース消費を抑えられますが、Collectorに障害が発生した場合にノード上の全アプリケーションが影響を受けます。コンテナオーケストレーション環境（Kubernetes、Amazon ECSなど）に適しています。

### ゲートウェイ

独立したホストまたはコンテナ群としてCollectorを集中配置するパターンです。

[f:id:mackerelio:20260522184015p:plain]

複数のホストやサービスからのテレメトリーデータを1箇所に集約できます。大規模な環境で、サンプリングやフィルタリングなどの加工を集中的に行いたい場合に適しています。ゲートウェイ自体が単一障害点にならないよう、冗長化を考慮する必要があります。

### パターンの組み合わせと選び方

これらの配置パターンは組み合わせて使うこともできます。例えば、各ノードにデーモンセットとしてCollectorを配置し、さらにゲートウェイCollectorで集約するといった構成が考えられます。

どのパターンを選ぶか迷った場合は、まずエージェント（同一ホスト）パターンから始めるのが最もシンプルです。Collectorを導入する際は、Mackerelオリジナルのディストリビューションである次節の **Mackerel Distro of OpenTelemetry (MDOT) Collector** を使うと、Mackerel向けの設定が同梱されているため手軽に始められます。

## Mackerel Distro of OpenTelemetry (MDOT) Collector

MackerelではOpenTelemetry Collectorのディストリビューションとして、[**Mackerel Distro of OpenTelemetry (MDOT) Collector**](https://github.com/mackerelio/opentelemetry-collector-mackerel/blob/main/README.ja.md)を提供しています。MDOT Collectorは、公式のOpenTelemetry Collectorをベースに、Mackerelで利用する際に必要な設定や推奨コンポーネントをあらかじめ組み込んだ、Mackerelオリジナルのディストリビューションです。

公式のCollectorをそのまま導入する場合と比べて、次のような特徴があります。

* **Mackerel向け設定が同梱**：Mackerel専用のOTLPエクスポーターを提供しており、Mackerelのエンドポイントや認証ヘッダーといった送信に必要な設定が初期状態で組み込まれています。APIキーを指定するだけで、すぐにMackerelへのデータ送信を開始できます。
* **Mackerelに最適化されたバッチ送信**：MDOTのOTLPエクスポーターは送信キュー（`sending_queue`）によるバッチ処理を初期状態で有効にしており、Mackerelのリクエストサイズ上限に合わせてバッチサイズが自動的に調整されます。追加の設定なしで安全に運用を開始できます。
* **公式版Collectorと互換**：OpenTelemetry Collectorの公式コンポーネントとMackerel独自のコンポーネントから構成されたディストリビューションであり、標準のCollectorと互換性があります。公式版から移行する場合は既存の設定をそのまま流用でき、独自のパイプラインを構築する場合も公式版と同じ要領で記述できます。

MDOT Collectorの導入手順については、[Mackerel OpenTelemetry コレクターのドキュメント](https://github.com/mackerelio/opentelemetry-collector-mackerel/blob/main/distributions/otelcol-mackerel/README.ja.md)を参照してください。

## 次のステップ

計装手法と環境構成を理解できたら、言語別のガイドに沿ってAPMを導入しましょう。

* [GoのアプリケーションからMackerelにトレースを送信する](https://mackerel.io/ja/docs/entry/tracing/installations/go)
* [Node.jsのアプリケーションからMackerelにトレースを送信する](https://mackerel.io/ja/docs/entry/tracing/installations/nodejs)
* [PHPのアプリケーションからMackerelにトレースを送信する](https://mackerel.io/ja/docs/entry/tracing/installations/php)
* [PythonのアプリケーションからMackerelにトレースを送信する](https://mackerel.io/ja/docs/entry/tracing/installations/python)
* [RubyのアプリケーションからMackerelにトレースを送信する](https://mackerel.io/ja/docs/entry/tracing/installations/ruby)
* [.NETのアプリケーションからMackerelにトレースを送信する](https://mackerel.io/ja/docs/entry/tracing/installations/dotnet)
* [その他の言語にOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/other-language)

ブラウザの計装やCollectorの導入については、以下のガイドを参照してください。

* [ブラウザにOpenTelemetryを導入する](https://mackerel.io/ja/docs/entry/tracing/installations/browser)
* [Mackerel OpenTelemetry コレクターを導入する](https://github.com/mackerelio/opentelemetry-collector-mackerel/blob/main/distributions/otelcol-mackerel/README.ja.md)

<style>.entry-content img.hatena-fotolife { border: none !important; }</style>
