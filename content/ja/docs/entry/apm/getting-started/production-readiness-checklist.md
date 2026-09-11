---
Title: Mackerel APM導入ガイド ― Mackerel APM 本番導入チェックリスト
Date: 2026-09-11T11:01:46+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/production-readiness-checklist
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293432
---

このチェックリストは、Mackerel APM の検証・導入ガイドシリーズを通じて取り組んできた内容をまとめたものです。本番導入に進む前に、各項目の完了状況を確認してみてください。

すべてにチェックが入っている必要はありません。状況に応じて必要な項目を優先し、段階的に進めてください。

## APMの基礎理解

参考：[Mackerel APMの基礎理解](./mackerel-apm-basics)

- APMの基本概念（メトリック・トレース・スパン）を理解した
- Mackerel APMの特徴（OpenTelemetry対応、分散トレーシング、REDメトリック）を把握した

## データ取得と送信構成

参考：[ゼロコード計装による最短データ取得](./zero-code-instrumentation) / [Mackerel OpenTelemetryコレクターによる送信設定](./otel-collector-setup)

- トライアルオーガニゼーションを作成した（本番オーガニゼーションへの意図しない課金を防止）
- Mackerel OpenTelemetryコレクターをセットアップした
- ゼロコード計装でアプリケーションからトレースを送信できた
- Mackerel APMの画面でトレースが表示されることを確認した

## 可視化とデータ分析

参考：[短期検証による可視化分析](./verification-analysis)

- REDメトリック（Rate、Error、Duration）でサービスの状態を把握した
- トレースの階層構造を展開し、スパンごとの処理時間を確認した
- パフォーマンスのボトルネックやエラーの発生箇所を特定してみた

## コストの見積もり

参考：[コスト・データ量の見極め](./cost-estimation)

- 検証データからスパン数を確認した
- 本番トラフィック比率で補正し、月間スパン数を概算した
- Mackerel APMの料金体系にあてはめ、月額コストの目安を算出した

## データ量・コストの最適化

参考：[フィルタリングとサンプリングによるデータ量・コストの最適化](./filtering-and-sampling)

- フィルタリングで不要なトレース（ヘルスチェックなど）を除外した
- サンプリング方式（ヘッドベース or テイルベース）を選定した
- 重要なトレース（エラー、スロークエリ）が引き続き確認できることを検証した
- 最適化後のスパン数とコスト見込みを再計算した

## 本番導入の準備

- コレクターのカスタム設定ファイル（config.yaml）を作成した
- 本番環境の構成パターン（サイドカー型 or ゲートウェイ型）を検討した
- 導入判断に必要な情報（可視化の価値、コスト見積もり、期待効果）を整理した
- 導入対象サービスと段階的な拡大計画を策定した

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/filtering-and-sampling" rel="prev">フィルタリングとサンプリングによるデータ量・コストの最適化</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/nodejs-zero-code-instrumentation" rel="next">Node.js におけるゼロコード計装</a></li>
</ul>
</nav>
