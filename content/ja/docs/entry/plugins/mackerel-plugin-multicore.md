---
Title: メトリックプラグイン - mackerel-plugin-multicore
Date: 2023-03-29T18:31:12+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-multicore
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889975939714
CustomPath: plugins/mackerel-plugin-multicore
---

mackerel-plugin-multicore はマルチコアの CPU を監視するためのプラグインです。[システムメトリック](https://mackerel.io/ja/docs/entry/spec/metrics) の一つである cpu の値はすべてのコアの合計値ですが、このプラグインではコアごとの使用状況をメトリックとして投稿できます。


[:contents]

<h2 id="metrics">監視できるメトリック</h2>

### MultiCore CPU

| メトリック表示名  | メトリック名                           | 差分  | 積み上げ表示 | 説明                                                      |
| --------- | -------------------------------- | --- | ------ | ------------------------------------------------------- |
| user      | custom.multicore.cpu.#.user      | -   | ◯      | ユーザーモードで使用した時間                                          |
| nice      | custom.multicore.cpu.#.nice      | -   | ◯      | 優先順位の低いユーザーモードで使用した時間（nice）                             |
| system    | custom.multicore.cpu.#.system    | -   | ◯      | システムモードで使用した時間                                          |
| idle      | custom.multicore.cpu.#.idle      | -   | ◯      | アイドル状態の時間                                               |
| iowait    | custom.multicore.cpu.#.iowait    | -   | ◯      | I/O が完了するまでの待ち時間                                        |
| irq       | custom.multicore.cpu.#.irq       | -   | ◯      | 割り込みの処理に使用した時間                                          |
| softirq   | custom.multicore.cpu.#.softirq   | -   | ◯      | ソフト割り込みの処理に使用した時間                                       |
| steal     | custom.multicore.cpu.#.steal     | -   | ◯      | 仮想化環境において、ゲスト OS がホスト OS やハイパーバイザから CPU を割り当ててもらえなかった時間 |
| guest     | custom.multicore.cpu.#.guest     | -   | ◯      | ゲスト OS が使用した時間                                          |
| guestNice | custom.multicore.cpu.#.guestNice | -   | ◯      | nice が適用されたゲスト OS が使用した時間                               |

/proc/stat の cpu0 などの各値を取得。# は cpu0 などに置き換えられます。

### MultiCore loadavg5 per core

| メトリック表示名 | メトリック名 | 差分 | 積み上げ表示 | 説明 |
| --- | --- | --- | --- | --- |
| loadavg5 | custom.multicore.loadavg_per_core.loadavg5 | - | - | `loadavg5` の値を CPU コア数で割った値 |

/proc/loadavg の loadavg5 の値を取得。


<h2 id="options">指定可能なオプション</h2>

| オプション | 省略形 | 説明 | デフォルト値 |
| --- | --- | --- | --- | 
| --tempfile |  | tempfile の保存先ファイルパスの指定 |  |
| --help | -h | ヘルプを表示 |  |

* tempfile には最新の実行結果が記録されます
* デフォルトでは `/var/tmp/mackerel-agent` 配下に `mackerel-plugin-multicore` という名前で保存されます


<h2 id="config">エージェントへの設定例</h2>

```
[plugin.metrics.multicore]
command = ["mackerel-plugin-multicore"]
```

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-multicore
