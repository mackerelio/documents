---
Title: サポートされている PromQL の機能
Date: 2024-06-27T00:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/howto/labeled-metrics/promql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6801883189090642496
---

ラベル付きメトリックは、PromQL クエリ言語を用いてグラフに表示できます。Mackerel では、PromQL の以下の機能がサポートされています。

## 時系列セレクタ

- [ベクトルセレクタ](https://prometheus.io/docs/prometheus/latest/querying/basics/#instant-vector-selectors)
  - `=`（等号ラベルマッチャ）
  - `!=`（不等号ラベルマッチャ）
  - `=~`（正規表現ラベルマッチャ）
  - `!~`（否定正規表現ラベルマッチャ）
- [範囲ベクトルセレクタ](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors)

### 範囲ベクトルセレクタの制約事項

範囲ベクトルセレクタで指定した期間とグラフの表示期間が大きく異なる場合、グラフの表示が正しく行われないことがあります。

これには、PromQL で 5 分間の期間を指定（`sum(irate(nginx_http_requests_total[5m]))`）し、グラフの表示期間が 1 日の場合等が該当します。

この制限を回避するためには、範囲ベクトルセレクタで指定する期間でグラフの表示期間をできる限り近い値に指定してください。

### 修飾子

- [`offset`](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier)
- [`@`](https://prometheus.io/docs/prometheus/latest/querying/basics/#modifier)

## 集計演算子

- [`sum()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`avg()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`min()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`max()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`quantile()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`count()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`count_values()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)

また、集計に用いるラベルを制御する `by`・`without` 修飾子に対応しています。

## 二項演算子

`(vector) x (vector)`、`(vector) x (scalar)`、`(scalar) x (vector)`、`(scalar) x (scalar)` の 4 パターンで定義されます。

ベクトル同士の演算を実現するために、[ベクトルマッチング](https://prometheus.io/docs/prometheus/latest/querying/operators/#vector-matching)という仕組みが実装されています。一対一対応（算術・比較演算子）と多対多対応（集合演算子）は既に実装が完了しています。

`group_left`・`group_right` 修飾子を用いた一対多対応は未実装です。

### 算術演算子

- [`+`（加算）](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`-`（減算）](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`\*`（乗算）](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`/`（除算）](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`%`（剰余算）](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`^`（冪乗）](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)

### 比較演算子

- [`==`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`!=`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`>`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`>=`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`<`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`<=`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)

#### 集合演算子

- [`and`（積集合）](https://prometheus.io/docs/prometheus/latest/querying/operators/#logical-set-binary-operators)
- [`or`（和集合）](https://prometheus.io/docs/prometheus/latest/querying/operators/#logical-set-binary-operators)
- [`unless`（差集合）](https://prometheus.io/docs/prometheus/latest/querying/operators/#logical-set-binary-operators)

## 関数

### カウンタ用の計算関数

- [`irate()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)
- [`increase()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#increase)

### `<aggregation>_over_time` 関数

- [`avg_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`sum_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`count_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`last_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`present_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)

### ラベル関数

- [`label_join()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#label_join)
- [`label_replace()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#label_replace)

### 型変換関数

- [`vector()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#vector)
- [`scalar()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#scalar)
