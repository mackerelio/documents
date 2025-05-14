---
Title: トレース - 料金
Date: 2025-03-13T16:34:12+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/guide/pricing-mackerel
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333959028
---

月初から月末までの1ヶ月間に投稿されたスパン数に対して費用が発生します。詳しくは [料金ページ](https://ja.mackerel.io/pricing) や [利用料金算出方法](https://support.mackerel.io/hc/ja/articles/31304727432729-%E5%88%A9%E7%94%A8%E6%96%99%E9%87%91%E7%AE%97%E5%87%BA%E6%96%B9%E6%B3%95) をご参照ください。

[:contents]

### Span数について

Span とは、OpenTelemetry が送信する分散トレーシングに用いられるデータの単位です。

1 つの Span が1つの操作を表します。
例えば、SQL を実行したり、リクエストを受けたりするとそれぞれ 1 つ Span が作られます。

具体的には下の図の線 1 本 1 本が 1 Span を表します。

<figure class="figure-image figure-image-fotolife" title="Issues画面 / Trace">[f:id:mackerelio:20250305173125p:plain]<figcaption>Issues画面 / Trace</figcaption></figure>

### Span 数の推測

Span の数はアプリケーションの仕組みによって左右されるため、正確に推測することは難しいですが、一般的な Web アプリケーションの場合は以下のように推測することが可能です。

* モノリシックな REST API 用のサーバーを使用している場合、概ね 1 リクエストで 10 〜 20 の Spanを作成します。
* 1 リクエスト平均 20 Span 作成すると仮定すると、5 万リクエスト毎に 100 万 Span 作成されます。
* この値にリクエスト数を掛けることで、Span 数を推測することができます。
