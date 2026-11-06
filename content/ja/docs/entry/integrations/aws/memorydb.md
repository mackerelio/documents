---
Title: AWSインテグレーション - MemoryDB
Date: 2026-11-06T15:23:24+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/memorydb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14729835035334676294
CustomPath: integrations/aws/memorydb
---

MackerelはAWSインテグレーションにてAmazon MemoryDBのメトリック取得や監視に対応しています。1クラスターを1ホストとして登録し、課金対象として1クラスター = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧、必要な権限については、[AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)をご確認ください。

## 取得メトリック

AWSインテグレーションのMemoryDB対応で取得できるメトリックは以下の通りです。メトリックの詳細はAWSのヘルプをご確認ください。

- [ホストレベルのメトリクス](https://docs.aws.amazon.com/ja_jp/memorydb/latest/devguide/metrics.HostLevel.html)
- [MemoryDBのメトリクス](https://docs.aws.amazon.com/ja_jp/memorydb/latest/devguide/metrics.memorydb.html)

通常はノードごとに最大で26個のメトリックが取得されます。データ階層化が有効な場合はノードごとに最大で32個のメトリックが取得されます。

### 通常のメトリック

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|CPU|CPUUtilization<br>EngineCPUUtilization|memorydb.cpu.#.used<br>memorydb.cpu.#.engine|percentage|Average|
|Memory|FreeableMemory<br>SwapUsage<br>BytesUsedForMemoryDB|memorydb.memory.#.freeable<br>memorydb.memory.#.swap<br>memorydb.memory.#.used|bytes|Average|
|Database Memory Usage|DatabaseMemoryUsagePercentage|memorydb.memory_usage.#.database|percentage|Average|
|Database Capacity Usage|DatabaseCapacityUsagePercentage|memorydb.capacity_usage.#.capacity|percentage|Average|
|Evictions|Evictions|memorydb.evictions.#.evictions|integer|Average|
|Connections|CurrConnections|memorydb.connections.#.current|integer|Average|
|Network Traffic|NetworkBytesIn<br>NetworkBytesOut|memorydb.network_traffic.#.in<br>memorydb.network_traffic.#.out|bytes|Average|
|Network Peak Traffic|NetworkMaxBytesIn<br>NetworkMaxBytesOut|memorydb.network_max_bytes.#.in<br>memorydb.network_max_bytes.#.out|bytes|Average|
|Network Packets|NetworkPacketsIn<br>NetworkPacketsOut|memorydb.network_packets.#.in<br>memorydb.network_packets.#.out|integer|Average|
|Network Peak Packets|NetworkMaxPacketsIn<br>NetworkMaxPacketsOut|memorydb.network_max_packets.#.in<br>memorydb.network_max_packets.#.out|integer|Average|
|Network Allowance Exceeded|NetworkBandwidthInAllowanceExceeded<br>NetworkBandwidthOutAllowanceExceeded<br>NetworkConntrackAllowanceExceeded<br>NetworkPacketsPerSecondAllowanceExceeded|memorydb.network_allowance_exceeded.#.bandwidth_in<br>memorydb.network_allowance_exceeded.#.bandwidth_out<br>memorydb.network_allowance_exceeded.#.conntrack<br>memorydb.network_allowance_exceeded.#.packets_per_second|integer|Average|
|Request Latency [µs]|SuccessfulWriteRequestLatency<br>SuccessfulReadRequestLatency|memorydb.latency.#.successful_write<br>memorydb.latency.#.successful_read|float|Average|
|Replication|ReplicationBytes|memorydb.replication.#.bytes|bytes|Average|
|Replication Throughput|MaxReplicationThroughput|memorydb.replication_throughput.#.max|bytes/sec|Average|
|Replication Delayed Write Commands|ReplicationDelayedWriteCommands|memorydb.replication_delayed_write_commands.#.count|integer|Average|

### データ階層化が有効な場合の追加メトリック

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|MemoryDB Used Bytes|BytesUsedForMemoryDB (Tier=Memory)<br>BytesUsedForMemoryDB (Tier=SSD)|memorydb.bytes_used.#.memory<br>memorydb.bytes_used.#.ssd|bytes|Average|
|Disk IO Bytes|BytesReadFromDisk<br>BytesWrittenToDisk|memorydb.disk_io_bytes.#.read<br>memorydb.disk_io_bytes.#.write|bytes|Average|
|Disk IO Items|NumItemsReadFromDisk<br>NumItemsWrittenToDisk|memorydb.disk_io_items.#.read<br>memorydb.disk_io_items.#.write|integer|Average|

- 「Mackerel上のメトリック名」の`#`にはノード名が入ります。
