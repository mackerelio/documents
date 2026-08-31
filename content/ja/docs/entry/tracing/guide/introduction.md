---
Title: トレース - はじめに
Date: 2025-03-13T15:49:46+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/guide/introduction
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333958189
---

[:contents]

## Mackerelトレーシング機能とは

Mackerelトレーシング機能では、エラーや速度低下など、ユーザー体験の悪化原因を発見し、解決することができます。

Mackerelトレーシング機能ではトレースを14日間保存しています。

## Mackerelトレーシング機能で出来ること

Mackerelトレーシング機能の主な機能を紹介します。

### トレースの管理

トレース一覧画面では、時間ごとのレイテンシーの分布や、トレースの件数を確認できます。

<figure class="figure-image figure-image-fotolife" title="トレース一覧画面">[f:id:mackerelio:20260825171059p:plain]<figcaption>トレース一覧画面</figcaption></figure>

トレースの詳細画面では、発生時刻やスパンの詳細を確認できます。

<figure class="figure-image figure-image-fotolife" title="トレースの詳細画面">[f:id:mackerelio:20260825171052p:plain]<figcaption>トレースの詳細画面</figcaption></figure>


### 課題の管理

Mackerelトレーシング機能では、エラーが生じたリクエストを自動的に発見し、課題として登録します。

<figure class="figure-image figure-image-fotolife" title="課題一覧画面">[f:id:mackerelio:20260825171023p:plain]<figcaption>課題一覧画面</figcaption></figure>

課題の詳細画面では、スタックトレースやスパンの詳細を確認できます。

<figure class="figure-image figure-image-fotolife" title="課題の詳細画面">[f:id:mackerelio:20260825171112p:plain]<figcaption>課題の詳細画面</figcaption></figure>

具体的な機能の詳細は、以下のページを参照してください

[https://mackerel.io/ja/docs/entry/tracing/features:embed:cite]
