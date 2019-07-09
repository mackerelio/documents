---
Title: AWSインテグレーション - Redshift
Date: 2016-12-28T15:43:56+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/redshift
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687201742255
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/redshift/" target="_blank">Amazon Redshift</a>のメトリック取得や監視に対応しています。
AWSインテグレーションで連携をおこなった場合、課金対象として 1クラスター = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われます。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのRedshift対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/redshift/latest/mgmt/metrics-listing.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `24 + 2 × (キュー数) + 1 × (サービスクラス数) + 10 × (ノード数)` 個のメトリックが取得されます。

### クラスターごとのメトリック
メトリック名の `WLM_ID` にワークロード管理(WLM)キューのIDが入ります。 (例: 1, Default など)<br>
メトリック名の `SERVICE_CLASS` にワークロード管理(WLM)サービスクラスのIDが入ります。 (例: 6, 7 など)

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
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

### ノードごとのメトリック
Redshiftではクラスターごとに複数のノードを持つことができるので、それぞれのメトリックは以下のようにグルーピングされます。メトリック名の `NODE_ROLE` にノードのRoleが入ります。 (例: leader, compute_0 など)

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|CPU per Node|CPUUtilization|redshift.cpu_per_node.NODE_ROLE.used|percentage|Average|
|Network Throughput per Node|NetworkReceiveThroughput<br>NetworkTransmitThroughput|redshift.network_throughput_per_node.NODE_ROLE.receive<br>redshift.network_throughput_per_node.NODE_ROLE.transmit|bytes/sec|Average|
|Disk Space per Node|PercentageDiskSpaceUsed|redshift.disk_per_node.NODE_ROLE.used|percentage|Average|
|Disk IOPS|ReadIOPS<br>WriteIOPS|redshift.diskiops.NODE_ROLE.read<br>redshift.diskiops.NODE_ROLE.write|iops|Average|
|Disk Latency|ReadLatency<br>WriteLatency|redshift.latency.NODE_ROLE.read<br>redshift.latency.NODE_ROLE.write|float|Average|
|Disk Throughput|ReadThroughput<br>WriteThroughput|redshift.throughput.NODE_ROLE.read<br>redshift.throughput.NODE_ROLE.write|bytes/sec|Average|

<h2 id="notes">注意事項</h2>

AWSインテグレーションにより取得可能な上記のグラフ・メトリックのうち、下記のグラフに含まれるメトリックについては、メトリックの取得間隔が異なります。

- 5分間隔
  - Query Runtime Breakdown
  - Query Duration
  - Query Throughput
  - WLM Query Duration
  - WLM Query Throughput
