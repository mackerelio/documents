---
Title: トレース - はじめに
Date: 2025-03-13T15:49:46+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/guide/introduction
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333958189
---

[:contents]

## Mackerelトレーシング機能（旧：Vaxila）とは

Mackerelトレーシング機能では、エラーや速度低下など、ユーザー体験の悪化原因を発見し、解決することができます。

## Mackerelトレーシング機能で出来ること

Mackerelトレーシング機能の主な機能を紹介します

### Issueの管理

[f:id:mackerelio:20250305172220p:plain]<figcaption>Issue List 画面</figcaption></figure></p></figcaption></figure>

* Mackerelトレーシング機能では、エラーや遅いリクエストを自動的に発見します。

<figure class="figure-image figure-image-fotolife" title="Issue 画面">[f:id:mackerelio:20250305172249p:plain]<figcaption>Issue 画面</figcaption></figure>

* Issueの詳細画面では、Stacktraceや発生原因の推測などを把握することができます。

### Traceの管理

<figure class="figure-image figure-image-fotolife" title="Trace List 画面">[f:id:mackerelio:20250305172303p:plain]<figcaption>Trace List 画面</figcaption></figure>

* Mackerelトレーシング機能ではトレースを3日間保管しています。

<figure class="figure-image figure-image-fotolife" title="Trace List 画面">[f:id:mackerelio:20250305172333p:plain]<figcaption>Trace List 画面</figcaption></figure>

* トレースの遅延の時間ごとの分布を見ることができます。

<figure class="figure-image figure-image-fotolife" title="Trace 画面">[f:id:mackerelio:20250305172406p:plain]<figcaption>Trace 画面</figcaption></figure>

* トレースには発生時刻やSpanの内容が記録されています。

### SLOの管理 <a href="#slo_management" id="slo_management"></a>

<figure class="figure-image figure-image-fotolife" title="Dashboard 画面">[f:id:mackerelio:20250305172420p:plain]<figcaption>Dashboard 画面</figcaption></figure>

* SLOを管理し、一覧することができます

<figure class="figure-image figure-image-fotolife" title="SLO 画面">[f:id:mackerelio:20250305172452p:plain]<figcaption>SLO 画面</figcaption></figure>

* SLOのバジェットや推移を見ることができます



具体的な機能の詳細は、以下のページを参照してください

[https://mackerel.io/ja/docs/entry/tracing/features:embed:cite]
