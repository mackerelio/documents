---
Title: AWSインテグレーション - OpenSearch Service
Date: 2019-01-18T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/es
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/98012380832127355
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/opensearch-service/" target="_blank">Amazon OpenSearch Service</a>のメトリック取得や監視に対応しています。
AWSインテグレーションで連携を行なった場合、課金対象として1ドメイン = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われます。

> Amazon Elasticsearch Serviceが2021年9月に名称をAmazon OpenSearch Serviceに変更したことを受けて、MackerelのAWSインテグレーションも、名称をElasticsearch ServiceインテグレーションからOpenSearch Serviceインテグレーションに変更いたしました。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのOpenSearch Service対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/opensearch-service/latest/developerguide/managedomains-cloudwatchmetrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で129個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Cluster Status|ClusterStatus.green<br>ClusterStatus.yellow<br>ClusterStatus.red|es.cluster_status.green<br>es.cluster_status.yellow<br>es.cluster_status.red|integer|Minimum<br>Maximum<br>Maximum|
|Shards|Shards.active<br>Shards.unassigned<br>Shards.delayedUnassigned<br>Shards.activePrimary<br>Shards.initializing<br>Shards.relocating|es.shards.active<br>es.shards.unassigned<br>es.shards.delayedUnassigned<br>es.shards.activePrimary<br>es.shards.initializing<br>es.shards.relocating|integer|Sum|
|Nodes|Nodes|es.nodes.count|integer|Minimum|
|Searchable Documents|SearchableDocuments|es.searchable_documents.count|float|Average|
|Deleted Documents|DeletedDocuments|es.deleted_documents.count|float|Average|
|CPU Utilization|CPUUtilization|es.cpu_utilization.maximum<br>es.cpu_utilization.average|percentage|Maximum<br>Average|
|Free Storage Space|FreeStorageSpace|es.free_storage_space.maximum<br>es.free_storage_space.average<br>es.free_storage_space.minimum<br>es.free_storage_space.sum|bytes|Maximum<br>Average<br>Minimum<br>Sum|
|Cluster Used Space|ClusterUsedSpace|es.cluster_used_space.maximum<br>es.cluster_used_space.minimum|integer|Maximum<br>Minimum|
|Cluster Index Writes Blocked|ClusterIndexWritesBlocked|es.cluster_index_writes_blocked.blocked|integer|Maximum|
|JVM Memory Pressure|JVMMemoryPressure|es.jvm_memory_pressure.maximum|percentage|Maximum|
|JVM Old Gen Memory Pressure|OldGenJVMMemoryPressure|es.jvm_old_gen_memory_pressure.maximum|percentage|Maximum|
|Automated Snapshot Failure|AutomatedSnapshotFailure|es.automated_snapshot_failure.failure|integer|Maximum|
|CPU Credit Balance|CPUCreditBalance|es.cpu_credit_balance.balance|float|Minimum|
|Health Check [*1](#elasticsearch-only)|KibanaHealthyNodes<br>MasterReachableFromNode|es.health_check.kibana<br>es.health_check.master|integer|Minimum|
|OpenSearch Dashboards Healthy Nodes [*2](#opensearch-only)|OpenSearchDashboardsHealthyNodes|es.opensearch_dashboards_healthy_nodes.minimum<br>es.opensearch_dashboards_healthy_nodes.average<br>es.opensearch_dashboards_healthy_nodes.maximum|integer|Minimum<br>Average<br>Maximum|
|OpenSearch Dashboards Reporting Count [*2](#opensearch-only)|OpensearchDashboardsReportingFailedRequestSysErrCount<br>OpensearchDashboardsReportingFailedRequestUserErrCount<br>OpensearchDashboardsReportingRequestCount<br>OpensearchDashboardsReportingSuccessCount|es.opensearch_dashboards_reporting_count.sys_err<br>es.opensearch_dashboards_reporting_count.user_err<br>es.opensearch_dashboards_reporting_count.request<br>es.opensearch_dashboards_reporting_count.success|integer|Sum|
|KMS Key|KMSKeyError<br>KMSKeyInaccessible|es.kms_key.error<br>es.kms_key.inaccessible|integer|Maximum|
|Cluster Requests|ElasticsearchRequests [*1](#elasticsearch-only)<br>OpenSearchRequests [*2](#opensearch-only)<br>InvalidHostHeaderRequests|es.cluster_requests.count<br>es.cluster_requests.opensearch_count<br>es.cluster_requests.invalid_host_header|integer|Sum|
|Requests|2xx<br>3xx<br>4xx<br>5xx|es.requests.2xx<br>es.requests.3xx<br>es.requests.4xx<br>es.requests.5xx|integer|Sum|
|Throughput Throttle|ThroughputThrottle|es.throughput_throttle.minimum<br>es.throughput_throttle.maximum|integer|Minimum<br>Maximum|
|Master CPU Utilization|MasterCPUUtilization|es.master_cpu_utilization.master_cpu|percentage|Average|
|Master JVM Memory Pressure|MasterJVMMemoryPressure|es.master_jvm_memory_pressure.master_jvm|percentage|Maximum|
|Master JVM Old Gen Memory Pressure|MasterOldGenJVMMemoryPressure|es.master_jvm_old_gen_memory_pressure.master_jvm_old_gen|percentage|Maximum|
|Master CPU Credit Balance|MasterCPUCreditBalance|es.master_cpu_credit_balance.balance|float|Minimum|
|Master Sys Memory Utilization|MasterSysMemoryUtilization|es.master_sys_memory_utilization.maximum|percentage|Maximum|
|EBS Disk Queue Depth|DiskQueueDepth|es.ebs_disk_queue_depth.minimum<br>es.ebs_disk_queue_depth.average<br>es.ebs_disk_queue_depth.maximum|float|Minimum<br>Average<br>Maximum|
|EBS Burst Balance|BurstBalance|es.ebs_burst_balance.minimum<br>es.ebs_burst_balance.average<br>es.ebs_burst_balance.maximum|float|Minimum<br>Average<br>Maximum|
|EBS Latency|WriteLatency<br>ReadLatency|es.ebs_latency.#.minimum<br>es.ebs_latency.#.average<br>es.ebs_latency.#.maximum|float|Minimum<br>Average<br>Maximum|
|EBS Throughput|WriteThroughput<br>ReadThroughput|es.ebs_throughput.#.minimum<br>es.ebs_throughput.#.average<br>es.ebs_throughput.#.maximum|bytes/sec|Minimum<br>Average<br>Maximum|
|EBS Throughput Micro Bursting|WriteThroughputMicroBursting<br>ReadThroughputMicroBursting|es.ebs_throughput_micro_bursting.#.minimum<br>es.ebs_throughput_micro_bursting.#.average<br>es.ebs_throughput_micro_bursting.#.maximum|bytes/sec|Minimum<br>Average<br>Maximum|
|EBS IOPS|WriteIOPS<br>ReadIOPS|es.ebs_iops.#.minimum<br>es.ebs_iops.#.average<br>es.ebs_iops.#.maximum|iops|Minimum<br>Average<br>Maximum|
|EBS IOPS Micro Bursting|WriteIOPSMicroBursting<br>ReadIOPSMicroBursting|es.ebs_iops_micro_bursting.#.minimum<br>es.ebs_iops_micro_bursting.#.average<br>es.ebs_iops_micro_bursting.#.maximum|iops|Minimum<br>Average<br>Maximum|
|Instance Latency|IndexingLatency<br>SearchLatency|es.instance_latency.indexing<br>es.instance_latency.search|float|Average|
|Instance Rate|IndexingRate<br>SearchRate|es.instance_rate.indexing<br>es.instance_rate.search|float|Average|
|Instance Segment Count|SegmentCount|es.instance_segment_count.maximum<br>es.instance_segment_count.average|float|Maximum<br>Average|
|Instance Sys Memory Utilization|SysMemoryUtilization|es.instance_sys_memory_utilization.minimum<br>es.instance_sys_memory_utilization.average<br>es.instance_sys_memory_utilization.maximum|percentage|Minimum<br>Average<br>Maximum|
|JVM GC Collection Count|JVMGCYoungCollectionCount<br>JVMGCOldCollectionCount|es.jvm_gc_collection_count.young<br>es.jvm_gc_collection_count.old|integer|Maximum|
|JVM GC Collection Time|JVMGCYoungCollectionTime<br>JVMGCOldCollectionTime|es.jvm_gc_collection_time.young<br>es.jvm_gc_collection_time.old|integer|Maximum|
|OpenSearch Dashboards Concurrent Connections [*2](#opensearch-only)|OpenSearchDashboardsConcurrentConnections|es.opensearch_dashboards_concurrent_connections.count|integer|Maximum|
|OpenSearch Dashboards Heap [*2](#opensearch-only)|OpenSearchDashboardsHeapTotal<br>OpenSearchDashboardsHeapUsed|es.opensearch_dashboards_heap.total<br>es.opensearch_dashboards_heap.used|integer|Maximum|
|OpenSearch Dashboards Heap Utilization [*2](#opensearch-only)|OpenSearchDashboardsHeapUtilization|es.opensearch_dashboards_heap_utilization.heap|percentage|Maximum|
|OpenSearch Dashboards OS 1 Minute Load [*2](#opensearch-only)|OpenSearchDashboardsOS1MinuteLoad|es.opensearch_dashboards_os_1_minute_load.load|float|Average|
|OpenSearch Dashboards Request Total [*2](#opensearch-only)|OpenSearchDashboardsRequestTotal|es.opensearch_dashboards_request_total.total|integer|Sum|
|OpenSearch Dashboards Response Times Max In Millis [*2](#opensearch-only)|OpenSearchDashboardsResponseTimesMaxInMillis|es.opensearch_dashboards_response_times_max_in_millis.time_ms|integer|Maximum|
|Search Task Cancelled|SearchTaskCancelled<br>SearchShardTaskCancelled|es.search_task_cancelled.coordinator_node<br>es.search_task_cancelled.data_node|integer|Sum|
|Threadpool Index Threads|ThreadpoolIndexQueue<br>ThreadpoolIndexRejected<br>ThreadpoolIndexThreads|es.threadpool_index_threads.queue<br>es.threadpool_index_threads.rejected<br>es.threadpool_index_threads.threads|integer|Maximum|
|Threadpool Search Threads|ThreadpoolSearchQueue<br>ThreadpoolSearchRejected<br>ThreadpoolSearchThreads|es.threadpool_search_threads.queue<br>es.threadpool_search_threads.rejected<br>es.threadpool_search_threads.threads|integer|Maximum|
|Threadpool Force_merge Threads|ThreadpoolForce_mergeQueue<br>ThreadpoolForce_mergeRejected<br>ThreadpoolForce_mergeThreads|es.threadpool_force_merge_threads.queue<br>es.threadpool_force_merge_threads.rejected<br>es.threadpool_force_merge_threads.threads|integer|Maximum|
|Threadpool sql-worker Threads|Threadpoolsql-workerQueue<br>Threadpoolsql-workerRejected<br>Threadpoolsql-workerThreads|es.threadpool_sql_worker_threads.queue<br>es.threadpool_sql_worker_threads.rejected<br>es.threadpool_sql_worker_threads.threads|integer|Maximum|
|Threadpool Bulk Threads|ThreadpoolBulkQueue<br>ThreadpoolBulkRejected<br>ThreadpoolBulkThreads|es.threadpool_bulk_threads.queue<br>es.threadpool_bulk_threads.rejected<br>es.threadpool_bulk_threads.threads|integer|Maximum|
|Threadpool Write Threads|ThreadpoolWriteQueue<br>ThreadpoolWriteRejected<br>ThreadpoolWriteThreads|es.threadpool_write_threads.queue<br>es.threadpool_write_threads.rejected<br>es.threadpool_write_threads.threads|integer|Maximum|
|Coordinating Write Rejected|CoordinatingWriteRejected|es.coordinating_write_rejected.rejected|integer|Maximum|
|Primary Write Rejected|PrimaryWriteRejected|es.primary_write_rejected.rejected|integer|Maximum|
|Replica Write Rejected|ReplicaWriteRejected|es.replica_write_rejected.rejected|integer|Maximum|

<p id="elasticsearch-only">*1 Elasticsearch エンジンの場合のみ</p>
<p id="opensearch-only">*2 OpenSearch エンジンの場合のみ</p>

- "Mackerel上のメトリック名"の#には"Write"、"Read"のいずれかが入ります。

<h2 id="notes">注意事項</h2>

汎用または磁気 EBS ボリュームを使用する場合、EBS ボリュームのメトリクスは 5 分ごとのみに更新されます。
