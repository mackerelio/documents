---
Title: 用語集
Date: 2014-04-30T16:13:25+09:00
URL: https://mackerel.io/ja/docs/entry/glossary
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722986178
---

Mackerel で使用されている用語とその解説です。

## サービス

Mackerelにおいて、サービスとはホストを運用する上でもっとも大きな単位です（例: 「はてなブックマーク」「社内システム（勤怠管理）」など）。通常、複数のホストがそれぞれの「役割」（→ ロール）を担当し、協調しあってひとつのサービスを形づくります。関係しあうホストをひとつのサービスに属するものとしてまとめることで、ホストの管理・監視が容易になります。Mackerel における「サービス」については、[https://mackerel.io/ja/docs/entry/spec/about-service-role:title]も参照してください。

## ロール

ロールはサービスに所属するホストをさらにその「役割」（アプリケーション、データベース、etc…）によって分類する概念です。Mackerelではホストの状況をロールごとにまとめて可視化できます。Mackerel における「ロール」については、[https://mackerel.io/ja/docs/entry/spec/about-service-role:title]も参照してください。

<h2 id="host">ホスト</h2>

エージェントが動く OS インスタンスひとつ（仮想化されたものを含む）に対応します。ひとつのホストには複数のロール（およびサービス）を割り当てられます。Mackerel における「ホスト」については、[https://mackerel.io/ja/docs/entry/spec/about-host:title]も参照してください。

**ステータス**: ホストは standby/maintenance/working といった、ホストの状況を管理するためのステータスを持ちます。運用の際にこのステータスを更新することで、新規ホストのセットアップ〜サービスへの投入〜ホストの撤退というフローをMackerel上で実現できます。

**ホストID**: Mackerelは各ホストをホストIDで識別します。ホストIDはmackerel-agentが動作しているホスト上のファイル`/var/lib/mackerel-agent/id`に保存されています。そのため、`/var/lib/mackerel-agent/id`が別ホストにコピーされると実際は複数のホストがMackerel上では同一のホストと認識され、メトリックの投稿や監視が正しく動作しなくなりますので注意してください。

<h2 id="organization">オーガニゼーション</h2>

Mackerelを利用する会社・組織・個人を指します。Mackerelのユーザーアカウントはいずれかのオーガニゼーションに所属します。ホストやサービスの情報がオーガニゼーションの枠を越えて共有されることはありません。

他のオーガニゼーションに使用されている名前のオーガニゼーションを新たに作成することはできません。

また1つのオーガニゼーションに複数のユーザーが所属したり、1人のユーザーが個人用と会社用といった複数のオーガニゼーションに所属することもできます。

- → [他のユーザーをMackerelに招待する](https://mackerel.io/ja/docs/entry/howto/invite-others)

## メトリック

mackerel-agent が定期的に収集するホストの状況です。CPU・メモリの利用状況や、IOの量などが計測され、Mackerelのサーバに自動的に送信されます。ウェブ上でこれらの値をグラフィカルに確認できます。

sensuプラグイン互換のシンプルなプロトコルで、ユーザー定義のメトリックを登録することも可能です。

メトリクスとも表記されますが、Mackerelではメトリックで統一しています。

- → [ユーザー定義のメトリックを投稿するには](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)

## ラベル付きメトリック

ラベル付きメトリックは [OpenTelemetry](https://opentelemetry.io/) の仕様に準拠した機能です。OpenTelemetry のエコシステムを利用してメトリックを Mackerel に投稿できます。

Mackerel に投稿されたラベル付きメトリックは PromQL クエリ言語を用いてグラフに表示・監視することができます。

2024 年 2 月時点では、この機能はパブリックベータ版として提供されています。

- → [ラベル付きメトリックを Mackerel に投稿する](https://mackerel.io/ja/docs/entry/howto/labeled-metrics/post-metrics)
- → [クエリによる監視を行う](https://mackerel.io/ja/docs/entry/query-monitoring)
- → [クエリグラフを利用する](https://mackerel.io/ja/docs/entry/howto/labeled-metrics/query-graph)
- → [サポートされている PromQL の機能](https://mackerel.io/ja/docs/entry/howto/labeled-metrics/promql)
