---
Title: AWS Integration - MemoryDB
Date: 2026-11-06T15:23:18+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/memorydb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/14729835035334676228
CustomPath: integrations/aws/memorydb
---

Mackerel supports obtaining and monitoring Amazon MemoryDB metrics in AWS Integration. Each cluster is registered as one host and counts as one micro host for billing purposes. In addition, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

For AWS Integration configuration methods, a list of supported AWS services, and required permissions, refer to [AWS Integration](https://mackerel.io/docs/entry/integrations/aws).

## Obtaining metrics

The metrics obtainable with AWS Integration’s MemoryDB support are as follows. For details about these metrics, refer to the AWS documentation.

- [Host-Level Metrics](https://docs.aws.amazon.com/memorydb/latest/devguide/metrics.HostLevel.html)
- [Metrics for MemoryDB](https://docs.aws.amazon.com/memorydb/latest/devguide/metrics.memorydb.html)

Normally, up to 26 metrics are retrieved per node. When data tiering is enabled, up to 32 metrics are retrieved per node.

### Standard metrics

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
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

### Additional metrics when data tiering is enabled

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|MemoryDB Used Bytes|BytesUsedForMemoryDB (Tier=Memory)<br>BytesUsedForMemoryDB (Tier=SSD)|memorydb.bytes_used.#.memory<br>memorydb.bytes_used.#.ssd|bytes|Average|
|Disk IO Bytes|BytesReadFromDisk<br>BytesWrittenToDisk|memorydb.disk_io_bytes.#.read<br>memorydb.disk_io_bytes.#.write|bytes|Average|
|Disk IO Items|NumItemsReadFromDisk<br>NumItemsWrittenToDisk|memorydb.disk_io_items.#.read<br>memorydb.disk_io_items.#.write|integer|Average|

- `#` in "Metric name in Mackerel" is replaced by the node name.
