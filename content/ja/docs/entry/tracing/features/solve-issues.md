---
Title: トレース - 問題を素早く解決する
Date: 2025-03-13T15:55:33+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/features/solve-issues
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333961479
---

発生した問題をIssueにまとめ、Issueの原因を分析します。

[:contents]

### 問題の原因を発見する

OpenTelemetryによる分散トレーシングを使用することで、エラーが起きたときの状態を可視化します。

<figure class="figure-image figure-image-fotolife" title="Issues画面 / Trace">[f:id:mackerelio:20250305173457p:plain]<figcaption>Issues画面 / Trace</figcaption></figure>

同じ時間に起きた他のIssueも把握できるので、サービスを横断した問題の原因を探ることができます。

<figure class="figure-image figure-image-fotolife" title="Issues画面 / Concurrent issues">[f:id:mackerelio:20250305173511p:plain]<figcaption>Issues画面 / Concurrent issues</figcaption></figure>

### Issueを解決する

Issueのトレースとそれ以外のトレースを比較して自動で原因を探ります。

<figure class="figure-image figure-image-fotolife" title="Issues画面 / Estimated reasons">[f:id:mackerelio:20250305173522p:plain]<figcaption>Issues画面 / Estimated reasons</figcaption></figure>

例えば、以下の問題を探ります。

* 「user\_id が1のときにだけ問題が起きている」
* 「データベースに複数回アクセスしている (N+1問題が起きている)」



また、以下の画面のようにSpanに設定されたパラメータの分布を見ることもできます。

<figure class="figure-image figure-image-fotolife" title="Issues 画面 / Aggregate">[f:id:mackerelio:20250305173540p:plain]<figcaption>Issues 画面 / Aggregate</figcaption></figure>
