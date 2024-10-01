---
Title: Supported PromQL Features
Date: 2024-06-27T00:00:00+09:00
URL: https://mackerel.io/docs/entry/howto/labeled-metrics/promql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6801883189090642183
---

When using labeled metrics, you can display a graph using the PromQL query language. The following PromQL features are supported in Mackerel.

## Time series selector

- [Vector selector](https://prometheus.io/docs/prometheus/latest/querying/basics/#instant-vector-selectors)
  - `=` (equality label matcher)
  - `!=` (inequality label matcher)
  - `=~` (regular expression label matcher)
  - `!~` (negative regular expression label matcher)
- [Range vector selector](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors)

### Constraints on range vector selectors

When the period specified in the range vector selector is significantly different from the graph display period, the graph display may be incorrect.

For example, if a 5-minute period is specified in PromQL (`sum(irate(nginx_http_requests_total[5m]))`), and the graph display period is 1 day, this applies.

To avoid this limitation, specify a period in the range vector selector that is as close as possible to the graph display period.

### Modifiers

- [`offset`](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier)
- [`@`](https://prometheus.io/docs/prometheus/latest/querying/basics/#modifier)

## Aggregation operators

- [`sum()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`avg()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`min()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`max()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`quantile()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`count()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)
- [`count_values()`](https://prometheus.io/docs/prometheus/latest/querying/operators/#aggregation-operators)

Also, the `by` and `without` modifiers for controlling the labels used for aggregation are supported.

## Binary operators

Defined in 4 patterns: `(vector) x (vector)`, `(vector) x (scalar)`, `(scalar) x (vector)`, and `(scalar) x (scalar)`.

To perform vector operations, a mechanism called [vector matching](https://prometheus.io/docs/prometheus/latest/querying/operators/#vector-matching) is implemented. One-to-one correspondence (arithmetic and comparison operators) and many-to-many correspondence (set operators) have already been implemented.

One-to-many correspondence using the `group_left` and `group_right` modifiers is not implemented.

### Arithmetic operators

- [`+` (addition)](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`-` (subtraction)](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`*` (multiplication)](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`/` (division)](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`%` (remainder)](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)
- [`^` (exponentiation)](https://prometheus.io/docs/prometheus/latest/querying/operators/#arithmetic-binary-operators)

### Comparison operators

- [`==`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`!=`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`>`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`>=`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`<`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)
- [`<=`](https://prometheus.io/docs/prometheus/latest/querying/operators/#comparison-binary-operators)

### Logical/set binary operators

- [`and` (intersection)](https://prometheus.io/docs/prometheus/latest/querying/operators/#logical-set-binary-operators)
- [`or` (union)](https://prometheus.io/docs/prometheus/latest/querying/operators/#logical-set-binary-operators)
- [`unless` (difference)](https://prometheus.io/docs/prometheus/latest/querying/operators/#logical-set-binary-operators)

## Functions

### Counter Caluculation Functions

- [`irate()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)
- [`increase()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#increase)

### `<aggregation>_over_time` Functions

- [`avg_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`sum_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`count_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`last_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)
- [`present_over_time()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)

### Label Functions

- [`label_join()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#label_join)
- [`label_replace()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#label_replace)

### Type Conversion Functions

- [`vector()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#vector)
- [`scalar()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#scalar)
