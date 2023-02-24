---
Title: メトリック仕様
Date: 2014-05-26T17:41:51+09:00
URL: https://mackerel.io/ja/docs/entry/spec/metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815724654402
---

ホスト詳細画面やサービス詳細画面で表示されるメトリックの種類について解説します。

[:contents]

<h2 id="system-metric">システムメトリック</h2>
システムメトリックとは、mackerel-agent をインストールすると自動的にホストに投稿されるメトリック群です。


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
* memory（積み上げ。ただし `total`, `swap_total`, `swap_used`, `swap_cached`は折れ線）
  * Kernel 3.14以降のLinux
      * `memory.used`（total - available で算出した値）
      * `memory.available`
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
  * それ以外のLinux
      * `memory.free`
      * `memory.buffers`
      * `memory.cached`
      * `memory.used`（total - free - buffers - cached で算出した値）
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
* disk（折れ線）
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface（折れ線）
  * `interface.*.rxBytes.delta`（受信バイト数）
  * `interface.*.txBytes.delta`（送信バイト数）
* filesystem（折れ線）
  * `filesystem.*.size`
  * `filesystem.*.used`

また、mackerel-agent を用いた場合の上記それぞれのメトリックの取得元は以下の通りです。mackerel-agent のソースコードは [こちら](https://github.com/mackerelio/mackerel-agent) で公開していますので併せてご確認ください。

* loadavg
  * `/proc/loadavg` の内容をパースすることで取得
* cpu
  * `/proc/stat` の内容をパースすることで取得
* memory
  * `/proc/meminfo` の内容をパースすることで取得
* disk
  * `/proc/diskstats` の内容をパースすることで取得
* interface
  * `/proc/net/dev` の内容をパースすることで取得
* filesystem
  * `df` コマンドの実行結果をパースすることで取得

### Windows

processor queue length, cpu, memory, disk, interface, filesystem の6種類のグラフが表示され、それぞれ以下のメトリック名の値が描画されます。

* processor queue length（折れ線）
  * `processor_queue_length`
* cpu（積み上げ）
  * `cpu.user.percentage`
  * `cpu.system.percentage`
  * `cpu.idle.percentage`
* memory（積み上げ。ただし `total`, `pagefile_free`, `pagefile_total` は折れ線）
  * `memory.free`
  * `memory.used`
  * `memory.total`
  * `memory.pagefile_free`
  * `memory.pagefile_total`
* disk（折れ線）
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface（折れ線）
  * `interface.*.rxBytes.delta`（受信バイト数）
  * `interface.*.txBytes.delta`（送信バイト数）
* filesystem（折れ線）
  * `filesystem.*.size`
  * `filesystem.*.used`

また、mackerel-agent を用いた場合の上記それぞれのメトリックの取得元は以下の通りです。mackerel-agent のソースコードは [こちら](https://github.com/mackerelio/mackerel-agent) で公開していますので併せてご確認ください。

* processor queue length
  * Windows Performance Data Helper API によって情報を取得
* cpu
  * Windows Performance Data Helper API によって情報を取得
* memory
  * Win32 API である `GlobalMemoryStatusEx` を呼び出した結果から取得
* disk
  * WMI を使用して `Win32_PerfFormattedData_PerfDisk_LogicalDisk` クラスから情報を取得
* interface
  * Windows Performance Data Helper API によって情報を取得
* filesystem
  * Win32 API である以下の関数を呼び出した結果から取得し計算。
      * `GetLogicalDriveStringsW`
      * `QueryDosDeviceW`
      * `GetVolumeInformationW`
      * `GetDiskFreeSpaceExW`

<h2 id="user-defined-metric">カスタムメトリック</h2>

カスタムメトリックとは、プラグインや Mackerel API などを利用してユーザが任意でホストに投稿できる、システムメトリック以外のメトリックです。

* 例: `custom.foo`, `custom.foo.bar`
  * メトリック名の最後の `.`（ドット）までが共通するメトリックを、ひとつのグラフに投稿します。
  * メトリック名にはプレフィックスが必要です。mackerel-agent から投稿されるカスタムメトリックには `custom.` が自動的に付与されます。（バージョン 0.6.1 以降）
  * カスタムメトリック名の詳しい仕様については [こちら](https://mackerel.io/ja/api-docs/entry/host-metrics#post-graphdef) を参照してください。

カスタムメトリックを投稿する方法については下記を参照してください。

* [公式プラグインを利用してカスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins)
* [mackerel-agent からユーザー定義のカスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)
* [Mackerel API を利用してユーザー定義のカスタムメトリックを投稿する](https://mackerel.io/ja/api-docs/entry/host-metrics#post)

<h2 id="service-metric">サービスメトリック</h2>

サービスメトリックとは、特定のホストではなくサービスに紐付くメトリックです。[URL 外形監視](https://mackerel.io/ja/docs/entry/external-monitoring) をサービスに紐付けた際に可視化されるレスポンスタイムもサービスメトリックとして投稿されます。サービスメトリックのグラフは [各サービス](https://mackerel.io/my/services) の詳細画面で閲覧できます。

サービスメトリックを投稿する方法については [Mackerel API ドキュメント](https://mackerel.io/ja/api-docs/) の [サービスメトリックの投稿](https://mackerel.io/ja/api-docs/entry/service-metrics#post) を参照してください。
