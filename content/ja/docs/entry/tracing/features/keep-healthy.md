---
Title: トレース - サービスを健全に保つ
Date: 2025-03-13T15:55:13+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/features/keep-healthy
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333961467
---

サービスの状態を監視し健全な状態を保つことにより、ユーザー体験を向上させることができます

[:contents]

### サービスの状態を把握する

SLOの状態を把握することで、ユーザー体験に問題が無いことを確認できます。\
SLOはエラーの頻度やリクエスト遅延の閾値など、サービスの特性にあわせて設定することができます。

<figure class="figure-image figure-image-fotolife" title="Dashboard 画面">[f:id:mackerelio:20250305173326p:plain]<figcaption>Dashboard 画面</figcaption></figure>

個別のSLOの推移を見ることもできます

<figure class="figure-image figure-image-fotolife" title="SLO 画面">[f:id:mackerelio:20250305173341p:plain]<figcaption>SLO 画面</figcaption></figure>

### 問題を発見する

エラーや遅いリクエストを自動的に発見し、Issueとして一覧に追加します。 \
Slackの通知を有効化することで、問題が発生した場合すぐに気づけるようになります。

また、同じIssueはまとめられるので、大量のエラーに煩わせれることはありません。

<figure class="figure-image figure-image-fotolife" title="ssue List 画面">[f:id:mackerelio:20250305173355p:plain]<figcaption>ssue List 画面</figcaption></figure>

フィルタリング項目を設定することにより、Issueを詳細に検索することもできます。

<figure class="figure-image figure-image-fotolife" title="Issue List 画面 / Filtering">[f:id:mackerelio:20250305173411p:plain]<figcaption>Issue List 画面 / Filtering</figcaption></figure>
