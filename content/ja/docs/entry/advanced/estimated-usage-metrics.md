---
Title: 概算使用量メトリック
Date: 2026-03-01T00:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/estimated-usage-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901360112432
---

概算使用量メトリックは、Mackerelに投稿されたテレメトリーの量を計測し、[ラベル付きメトリック](https://mackerel.io/ja/docs/entry/glossary#ラベル付きメトリック)としてシステムが自動的に記録するメトリックです。

概算使用量メトリックは1分粒度で計測されており、メトリックエクスプローラーや[カスタムダッシュボード](https://mackerel.io/ja/docs/entry/howto/dashboard)で、リアルタイムでテレメトリー投稿量のグラフを閲覧できます。サービスや環境を区別する属性が付与されているため、サービス・環境を絞り込んで閲覧したり、異常が起こったサービス・環境を特定したりすることもできます。

また、[クエリによる監視](https://mackerel.io/ja/docs/entry/query-monitoring)を設定することで、意図しない投稿量の上昇や低下に気づくことができるようになります。

概算使用量メトリックは全てのオーガニゼーションに対し無償で提供されます。

※概算使用量メトリックの数値は、従量課金プランにおける請求対象の使用量と正確に一致することを保証しません。

## 概算使用量メトリックの一覧

現在、概算使用量メトリックは[トレーシング機能](https://mackerel.io/ja/docs/entry/tracing/guide/introduction)に対してのみ提供されています。

### 概算使用量メトリックで共通となるリソース属性

概算使用量メトリックにはリソース属性として以下のものが共通で付与されています。投稿元がMackerelであり、ユーザーのサービスではなくMackerel自体の状態を表す特殊なメトリックであることを表しています。

| キー                | 値の型 | 値                            |
| :------------------ | :----- | :---------------------------- |
| `service.name`      | string | `__mackerelio.system`（固定） |
| `service.namespace` | string | `__mackerelio`（固定）        |

### `__mackerelio.estimated_usage.monthly_spans`

[トレーシング機能](https://mackerel.io/ja/docs/entry/tracing/guide/introduction)に投稿されたトレースのスパン数を計測します。値はカウンタとして累積値を記録しており、月初にリセットされます。

| メトリック名                                 | 計装種別             | 単位     | 説明                                                                                           |
| :------------------------------------------- | :------------------- | :------- | :--------------------------------------------------------------------------------------------- |
| `__mackerelio.estimated_usage.monthly_spans` | Asynchronous Counter | `{span}` | The cumulative number of trace spans posted to Mackerel. Reset at the beginning of each month. |

**属性：**

| キー                                 | 値の型 | 説明                                                                                  | 値の例        |
| :----------------------------------- | :----- | :------------------------------------------------------------------------------------ | :------------ |
| `source.service.name`                | string | トレースのリソース属性`service.name`の値                                              | `http-server` |
| `source.service.namespace`           | string | トレースのリソース属性`service.namespace`の値                                         | `blog-site`   |
| `source.deployment.environment.name` | string | トレースのリソース属性`deployment.environment.name`または`deployment.environment`の値 | `production`  |

## PromQLによる集計例

概算使用量メトリックのグラフを描画したり、監視ルールを設定したりする場合には、[クエリグラフ機能](https://mackerel.io/ja/docs/entry/howto/labeled-metrics/query-graph)や[クエリによる監視機能](https://mackerel.io/ja/docs/entry/howto/labeled-metrics/query-graph)でPromQLのクエリを書く必要があります。

各種シナリオごとのPromQLの例を以下に提示しますので、運用の参考にしてください。

### これまで投稿されたスパン数の月ごとの累積値を知りたい

```
sum(__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system"})
```

### これまで投稿されたスパン数の月ごとの累積値を、OpenTelemetryのサービスごと区別して知りたい

```
sum by (source.service.name, source.service.namespace) (__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system"})
```

### これまで投稿されたスパン数の月ごとの累積値を、あるOpenTelemetryのサービスに絞って知りたい

`http-server`というサービスから送られたトレースのスパン数の累積値を見たい場合：

```
__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system", source.service.name="http-server"}
```

### 投稿された1分あたりのスパン数を知りたい

```
irate(__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system"}[$__rate_interval]) * 60
```
