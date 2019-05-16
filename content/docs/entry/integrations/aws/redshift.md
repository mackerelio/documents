---
Title: AWS Integration - Redshift
Date: 2017-01-11T18:55:11+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/redshift
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687205767320
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/redshift/" target="_blank">Amazon Redshift</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Table = 1 Micro Host.
In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.  <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics

The metrics obtainable with AWS Integration’s Redshift support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/redshift/latest/mgmt/metrics-listing.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is `24 + 2 × (number of queues) + 1 × (number of service classes) + 10 × (number of nodes)`.

### Metrics per Cluster
The `WLM_ID` of the metric name contains the workload management (WLM) queue ID. (Example: 1, Default etc.)<br>
The `SERVICE_CLASS` of the metric name contains the workload management (WLM) service class ID. (Example: 6, 7 etc.)

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|CPU|CPUUtilization|redshift.cpu.used|percentage|Average|
|Database Connections|DatabaseConnections|redshift.database_connections.used|integer|Average|
|Cluster Status|HealthStatus<br>MaintenanceMode|redshift.cluster_status.health<br>redshift.cluster_status.maintenance|integer|Average|
|Network Throughput|NetworkReceiveThroughput<br>NetworkTransmitThroughput|redshift.network_throughput.receive<br>redshift.network_throughput.transmit|bytes/sec|Average|
|Disk Space|PercentageDiskSpaceUsed|redshift.disk.used|percentage|Average|
|Total Table Count|TotalTableCount|redshift.total_table_count.count|integer|Average|
|Query Runtime Breakdown|QueryRuntimeBreakdown|redshift.query_runtime_breakdown.planning<br>redshift.query_runtime_breakdown.waiting<br>redshift.query_runtime_breakdown.executing_read<br>redshift.query_runtime_breakdown.executing_insert<br>redshift.query_runtime_breakdown.executing_delete<br>redshift.query_runtime_breakdown.executing_update<br>redshift.query_runtime_breakdown.executing_ctas<br>redshift.query_runtime_breakdown.executing_unload<br>redshift.query_runtime_breakdown.executing_copy<br>redshift.query_runtime_breakdown.commit|float|Average|
|Query Throughput|QueriesCompletedPerSecond|redshift.query_throughput.short<br>redshift.query_throughput.medium<br>redshift.query_throughput.long|float|Average|
|Query Duration|QueryDuration|redshift.query_duration.short<br>redshift.query_duration.medium<br>redshift.query_duration.long|float|Average|
|WLM Query Throughput|WLMQueriesCompletedPerSecond|redshift.wlm_query_throughput.WLM_ID|float|Average|
|WLM Query Duration|WLMQueryDuration|redshift.wlm_query_duration.WLM_ID|float|Average|
|WLM Queue Length|WLMQueueLength|redshift.wlm_queue_length.SERVICE_CLASS|integer|Average|

### Metrics per Node
Since there can be multiple Nodes per cluster in Redshift, each metric is grouped as follows. The metric name’s `NODE_ROLE` will contain the Role of the Node. (Example: leader, compute_0  etc.)

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|CPU per Node|CPUUtilization|redshift.cpu_per_node.NODE_ROLE.used|percentage|Average|
|Network Throughput per Node|NetworkReceiveThroughput<br>NetworkTransmitThroughput|redshift.network_throughput_per_node.NODE_ROLE.receive<br>redshift.network_throughput_per_node.NODE_ROLE.transmit|bytes/sec|Average|
|Disk Space per Node|PercentageDiskSpaceUsed|redshift.disk_per_node.NODE_ROLE.used|percentage|Average|
|Disk IOPS|ReadIOPS<br>WriteIOPS|redshift.diskiops.NODE_ROLE.read<br>redshift.diskiops.NODE_ROLE.write|iops|Average|
|Disk Latency|ReadLatency<br>WriteLatency|redshift.latency.NODE_ROLE.read<br>redshift.latency.NODE_ROLE.write|float|Average|
|Disk Throughput|ReadThroughput<br>WriteThroughput|redshift.throughput.NODE_ROLE.read<br>redshift.throughput.NODE_ROLE.write|bytes/sec|Average|

<h2 id="notes">Notes</h2>

Among the graphs/metrics obtainable with AWS integration mentioned above, the metric retrieval interval differs for metrics included in the following graph.

- 5 minute interval
  - Query Runtime Breakdown
  - Query Duration
  - Query Throughput
  - WLM Query Duration
  - WLM Query Throughput
