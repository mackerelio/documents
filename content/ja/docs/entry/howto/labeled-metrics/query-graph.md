---
Title: クエリグラフを利用する
Date: 2024-02-02T16:07:16+09:00
URL: https://mackerel.io/ja/docs/entry/howto/labeled-metrics/query-graph
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6801883189090642520
---

クエリグラフは、Mackerel に投稿されたラベル付きメトリックを PromQL クエリ言語を用いてグラフに表示する機能です。

ラベル付きメトリックとは、OpenTelemetry の仕様に準拠した Mackerel の新しい機能の名称です。OpenTelemetry とは、メトリック・ログ・トレースなどのテレメトリーデータを収集し、任意のバックエンドへエクスポートすることを目指して標準規格を定めたオープンソースのプロジェクトです。詳しくは OpenTelemetry のドキュメントをご覧ください。

https://opentelemetry.io/docs/

Mackerel にラベル付きメトリックを投稿する方法については、[ラベル付きメトリックを Mackerel に投稿する](https://mackerel.io/ja/docs/entry/howto/labeled-metrics/post-metrics) をご覧ください。

PromQL については、[サポートされている PromQL の機能](https://mackerel.io/ja/docs/entry/howto/labeled-metrics/promql) をご覧ください。

## クエリグラフを作成する

クエリグラフは [カスタムダッシュボード](https://mackerel.io/ja/docs/entry/howto/dashboard) のウィジェットとして利用できます。カスタムダッシュボードを作成・編集する画面で、グラフウィジェットのアイコンをドラッグ&ドロップして、ウィジェットを作成する位置を決定します。

[f:id:mackerelio:20181102173147p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。グラフウィジェットのアイコンがドラッグ&ドロップされている。]

グラフウィジェットを追加するダイアログが表示されたら、「グラフのタイプ」で「クエリ」を選択します。PromQL を記述するエディタが開き、上部のメニューからメトリック一覧を表示できます。

[f:id:mackerelio:20230901094723p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。クエリグラフの設定画面でメトリック一覧が表示されている。]

例として nginx.connections_current を選択してみると、以下のようにグラフが表示されます。

[f:id:mackerelio:20230901094745p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。クエリグラフの設定画面で nginx.connections_current が選択されていて、グラフが表示されている。]

<h2 id="setting-the-legend-of-the-graph">グラフの凡例を設定する</h2>

デフォルトでは、凡例はクエリとラベルを結合したものが設定されます。`nginx.connections_current` のクエリで表示されるグラフの凡例は、以下のように表示されていることがわかります。

- `nginx.connections_current{state="active"}`
- `nginx.connections_current{state="waiting"}`
- `nginx.connections_current{state="writing"}`
- `nginx.connections_current{state="reading"}`

[f:id:mackerelio:20230906092815p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。クエリグラフの設定画面で nginx.connections_current が選択されていて、グラフのラインにマウスカーソルがホバーされている。マウスカーソルの上にはグラフの凡例が表示されている。nginx.connections_current{state=&quot;active&quot;} - 13.00、nginx.connections_current{state=&quot;waiting&quot;} - 10.00、nginx.connections_current{state=&quot;writing&quot;} - 3.00、nginx.connections_current{state=&quot;reading&quot;} - 0.00]

ラベルの数が多くなると凡例の長さも非常に長くなり、グラフのデータポイントがどのラベルに対応するものなのかわからなくなってしまうかもしれません。グラフの凡例を設定することで、凡例の表示方法を変更できます。

設定画面の「凡例」から「値」のセレクトボックスを選択すると、現在のクエリに対応するラベルの一覧が表示されます。ここでは `state` を選択してみましょう。

[f:id:mackerelio:20230901094816p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。クエリグラフの設定画面でグラフの凡例の設定画面が表示されている。値のセレクトボックスには state が表示されていて、state が選択されている]

セレクトボックスで選択された値は「凡例」の「表示」に `{{state}}` と表示されます。これは凡例のテンプレート構文であり、`{{}}` で囲った値がラベルのキーとして存在する場合、その値に置き換えられます。今回は `state` がラベルのキーとして存在するため、`{{state}}` が `active` といった値に置き換えられます。

[f:id:mackerelio:20230901094839p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。クエリグラフの設定画面で nginx.connections_current が選択されていて、グラフのラインにマウスカーソルがホバーされている。マウスカーソルの上にはグラフの凡例が表示されている。active - 13.00、waiting - 10.00、writing - 3.00、reading - 0.00]

## 数値ウィジェットを作成する

ラベル付きメトリックを利用して数値ウィジェットを作成することもできます。数値ウィジェットのアイコンをドラッグ&ドロップして、ウィジェットを作成する位置を決定します。

[f:id:mackerelio:20231128213955p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。数値ウィジェットのアイコンがドラッグ&ドロップされている。]

数値ウィジェットを追加するダイアログが表示されたら、「メトリックのタイプ」で「クエリ」を選択します。数値ウィジェットでは、メトリックの系列を必ず 1 つに絞り込む必要があります。系列を絞り込むには以下の2つの方法があります。

- PromQL の `sum` や `avg` などの集約関数を利用して絞り込む
- ラベルのフィルタリングにより絞り込む

以下の例では、`state` ラベルの値が `active` の系列を絞り込んでいます。

[f:id:mackerelio:20240202120456p:plain:alt=Mackerel のウェブコンソールのスクリーンショット。数値ウィジェットの設定画面でクエリが選択されていて、エディタには nginx.connections_current{state=&quot;active&quot;} が記述されている。preview には 6.00 と表示されている。]
