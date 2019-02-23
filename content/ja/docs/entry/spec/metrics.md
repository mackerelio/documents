---
Title: メトリック仕様
Date: 2014-05-26T17:41:51+09:00
URL: https://mackerel.io/ja/docs/entry/spec/metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815724654402
---

サービス詳細及び、ホスト詳細画面で表示されるメトリックを解説します。

## システムメトリック
mackerel-agent をインストール・起動することで自動的に収集・投稿されるメトリック群です。

### Linux

loadavg, cpu, memory, disk, interface, filesystem の6種類のグラフが表示され、それぞれ以下のメトリック名の値が描画されます。

* loadavg（折れ線）
  * `loadavg1`
  * `loadavg5`
  * `loadavg15`
* cpu（積み上げ）
  * `cpu.user.percentage`
  * `cpu.iowait.percentage`
  * `cpu.system.percentage`
  * `cpu.idle.percentage`
  * `cpu.nice.percentage`
  * `cpu.irq.percentage`
  * `cpu.softirq.percentage`
  * `cpu.steal.percentage`
  * `cpu.guest.percentage`
* memory（積み上げ。`memory.total`、`swap_total` `swap_used`と`swap_cached`は折れ線）
  * Kernel 3.14以降のLinux
      * `memory.used`
      * `memory.available`
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
  * それ以外のLinux
      * `memory.free`
      * `memory.buffers`
      * `memory.cached`
      * `memory.used`
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
* disk（折れ線）
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface（折れ線）
  * `interface.*.rxBytes.delta` (受信バイト数)
  * `interface.*.txBytes.delta` (送信バイト数)
* filesystem（折れ線）
  * `filesystem.*.size`
  * `filesystem.*.used`

### Windows

processor queue length, cpu, memory, disk, interface, filesystem の6種類のグラフが表示され、それぞれ以下のメトリック名の値が描画されます。

* processor queue length（折れ線）
  * `processor_queue_length`
* cpu（積み上げ）
  * `cpu.user.percentage`
  * `cpu.system.percentage`
  * `cpu.idle.percentage`
* memory（積み上げ。`memory.total`、`pagefile_free`と`pagefile_total`は折れ線）
  * `memory.free`
  * `memory.used`
  * `memory.total`
  * `memory.pagefile_free`
  * `memory.pagefile_total`
* disk（折れ線）
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface（折れ線）
  * `interface.*.rxBytes.delta` (受信バイト数)
  * `interface.*.txBytes.delta` (送信バイト数)
* filesystem（折れ線）
  * `filesystem.*.size`
  * `filesystem.*.used`

<h2 id="user-defined-metric">ユーザ定義のメトリック（カスタムメトリック）</h2>

* `custom.foo(.bar)*.*`
  * 名前の最後のドットまでが共通するメトリックがひとつのグラフにまとめられ表示されます。

mackerel-agentから送信するカスタムメトリックには、プレフィックスとして`custom.`が付与されます（バージョン 0.6.1 より）。 mackerel-agentのアップデートにより標準メトリックが追加されることがありますので、APIを直接使用する場合も、`custom.`を付与することを推奨します。[ホストのカスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)も参照して下さい。
