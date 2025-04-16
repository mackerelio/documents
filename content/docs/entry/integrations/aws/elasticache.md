---
Title: AWS Integration - ElastiCache
Date: 2017-01-11T17:38:06+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/elasticache
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687205749311
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/elasticache/" target="_blank">Amazon ElastiCache</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Node = 1 Micro Host.
In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.  <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics

The metrics obtainable with AWS Integration’s ElastiCache support are as follows. For `Metric` explanations, refer to the AWS help pages (<a href="https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheMetrics.HostLevel.html" target="_blank">host level metrics</a>, <a href="https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/CacheMetrics.Memcached.html" target="_blank">Memcached</a>, and <a href="https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheMetrics.Redis.html" target="_blank">Redis / Valkey</a>).

The maximum number of metrics obtainable is 48 with Memcached, 70 with Redis and 89 with Valkey.

### Host level metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|CPU|CPUUtilization|elasticache.cpu.used|percentage|Average|
|CPU Credit|CPUCreditBalance<br>CPUCreditUsage|elasticache.cpu_credit.balance<br>elasticache.cpu_credit.used|float|Average|
|Swap Usage|SwapUsage|elasticache.swap.usage|bytes|Average|
|Freeable Memory|FreeableMemory|elasticache.memory.freeable|bytes|Average|
|Network Traffic|NetworkBytesIn<br>NetworkBytesOut|elasticache.network.in<br>elasticache.network.out|bytes|Average|
|Network Packets|NetworkPacketsIn<br>NetworkPacketsOut|elasticache.network_packets.in<br>elasticache.network_packets.out|integer|Sum|
|Network Allowance Exceeded|NetworkBandwidthInAllowanceExceeded<br>NetworkBandwidthOutAllowanceExceeded<br>NetworkConntrackAllowanceExceeded<br>NetworkLinkLocalAllowanceExceeded<br>NetworkPacketsPerSecondAllowanceExceeded|network_allowance_exceeded.in<br>network_allowance_exceeded.out<br>network_allowance_exceeded.conntrack<br>network_allowance_exceeded.link_local<br>network_allowance_exceeded.pps<br>|integer|Average|

### Memcached metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Commands|CmdGet<br>CmdSet<br>CmdFlush<br>CmdTouch<br>CmdConfigGet<br>CmdConfigSet|elasticache.memcached.commands.get<br>elasticache.memcached.commands.set<br>elasticache.memcached.commands.flush<br>elasticache.memcached.commands.touch<br>elasticache.memcached.commands.config_get<br>elasticache.memcached.commands.config_set|float|Average|
|Cache Hits/Misses|GetHits<br>GetMisses<br>DeleteHits<br>DeleteMisses<br>IncrHits<br>IncrMisses<br>DecrHits<br>DecrMisses<br>CasBadval<br>CasHits<br>CasMisses<br>TouchHits<br>TouchMisses|elasticache.memcached.cache.get_hits<br>elasticache.memcached.cache.get_misses<br>elasticache.memcached.cache.delete_hits<br>elasticache.memcached.cache.delete_misses<br>elasticache.memcached.cache.incr_hits<br>elasticache.memcached.cache.incr_misses<br>elasticache.memcached.cache.decr_hits<br>elasticache.memcached.cache.decr_misses<br>elasticache.memcached.cache.cas_badval<br>elasticache.memcached.cache.cas_hits<br>elasticache.memcached.cache.cas_misses<br>elasticache.memcached.cache.touch_hits<br>elasticache.memcached.cache.touch_misses|float|Average|
|Evictions|Evictions|elasticache.memcached.evictions.evictions|float|Average|
|Unfetched|EvictedUnfetched<br>ExpiredUnfetched|elasticache.memcached.unfetched.evicted<br>elasticache.memcached.unfetched.expired|float|Average|
|Traffics|BytesReadIntoMemcached<br>BytesWrittenOutFromMemcached|elasticache.memcached.traffics.bytes_read_into_memcached<br>elasticache.memcached.traffics.bytes_written_out_from_memcached|bytes|Average|
|Connections|CurrConnections<br>NewConnections|elasticache.memcached.connections.current<br>elasticache.memcached.connections.new|float|Average|
|Items|CurrItems<br>NewItems<br>Reclaimed<br>CurrConfig<br>SlabsMoved|elasticache.memcached.items.current<br>elasticache.memcached.items.new<br>elasticache.memcached.items.reclaimed<br>elasticache.memcached.items.current_config<br>elasticache.memcached.items.slabs_moved|float|Average|
|Memory|UnusedMemory<br>BytesUsedForHash<br>BytesUsedForCacheItems|elasticache.memcached.memory.unused<br>elasticache.memcached.memory.used_for_hash<br>elasticache.memcached.memory.used_for_cache_items|bytes|Average|

### Redis metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Commands|GetTypeCmds<br>SetTypeCmds<br>KeyBasedCmds<br>StringBasedCmds<br>HashBasedCmds<br>ListBasedCmds<br>SetBasedCmds<br>ClusterBasedCmds<br>SortedSetBasedCmds<br>EvalBasedCmds<br>GeoSpatialBasedCmds<br>HyperLogLogBasedCmds<br>PubSubBasedCmds<br>StreamBasedCmds|elasticache.redis.commands.get_type<br>elasticache.redis.commands.set_type<br>elasticache.redis.commands.key_based<br>elasticache.redis.commands.string_based<br>elasticache.redis.commands.hash_based<br>elasticache.redis.commands.list_based<br>elasticache.redis.commands.set_based<br>elasticache.redis.commands.cluster_based<br>elasticache.redis.commands.sorted_set_based<br>elasticache.redis.commands.eval_based<br>elasticache.redis.commands.geo_spatial_based<br>elasticache.redis.commands.hyper_log_log_based<br>elasticache.redis.commands.pub_sub_based<br>elasticache.redis.commands.stream_based|float|Average|
|Cache Hits/Misses|CacheHits<br>CacheMisses|elasticache.redis.cache.cache_hits<br>elasticache.redis.cache.cache_misses|float|Average|
|Evictions|Evictions|elasticache.redis.evictions.evictions|float|Average|
|Failures|AuthenticationFailures<br>CommandAuthorizationFailures<br>KeyAuthorizationFailures|elasticache.redis.failures.authentication<br>elasticache.redis.failures.command_authorization<br>elasticache.redis.failures.key_authorization<br>|integer|Sum|
|Memory|BytesUsedForCache|elasticache.redis.memory.used_for_cache|bytes|Average|
|Redis CPU|EngineCPUUtilization|elasticache.redis.cpu.engine|percentage|Average|
|Disk IO Bytes|BytesReadFromDisk<br>BytesWrittenToDisk|elasticache.redis.disk_io_bytes.read<br>elasticache.redis.disk_io_bytes.write|bytes|Average|
|Disk IO Items|NumItemsReadFromDisk<br>NumItemsWrittenToDisk|elasticache.redis.disk_io_items.read<br>elasticache.redis.disk_io_items.write|float|Average|
|Is Primary|IsPrimary (IsMaster)|elasticache.redis.is_primary.primary|integer|Average|
|Primary Link Health Status|PrimaryLinkHealthStatus (MasterLinkHealthStatus)|elasticache.redis.primary_link_health_status.sync|integer|Average|
|Keys Tracked|KeysTracked|elasticache.redis.keys_tracked.count|integer|Sum|
|Connections|CurrConnections<br>NewConnections|elasticache.redis.connections.current<br>elasticache.redis.connections.new|float|Average|
|Items|CurrItems<br>CurrVolatileItems<br>Reclaimed|elasticache.redis.items.current<br>elasticache.redis.items.current_volatile<br>elasticache.redis.items.reclaimed|float|Average|
|Active Defrag|ActiveDefragHits|elasticache.redis.active_defrag.hits|integer|Sum|
|Cache Hit Rate|CacheHitRate|elasticache.redis.cache_hit_rate.hit|percentage|Average|
|Memory Percentage|DatabaseMemoryUsagePercentage|elasticache.redis.memory_percentage.database|percentage|Average|
|Average TTL|DB0AverageTTL|elasticache.redis.average_ttl.db0|float|Average|
|Fragmentation Ratio|MemoryFragmentationRatio|elasticache.redis.fragmentation_ratio.memory|float|Average|
|Replication Bytes|ReplicationBytes|elasticache.redis.replication_bytes.replication|bytes|Average|
|Replication Lag|ReplicationLag|elasticache.redis.replication_lag.lag|float|Average|
|Save In Progress|SaveInProgress|elasticache.redis.save_in_progress.bgsave|integer|Maximum|
|Commands Latency|GetTypeCmdsLatency<br>SetTypeCmdsLatency<br>KeyBasedCmdsLatency<br>StringBasedCmdsLatency<br>HashBasedCmdsLatency<br>ListBasedCmdsLatency<br>SetBasedCmdsLatency<br>ClusterBasedCmdsLatency<br>SortedSetBasedCmdsLatency<br>EvalBasedCmdsLatency<br>GeoSpatialBasedCmdsLatency<br>HyperLogLogBasedCmdsLatency<br>PubSubBasedCmdsLatency<br>StreamBasedCmdsLatency|elasticache.redis.commands_latency.get_type<br>elasticache.redis.commands_latency.set_type<br>elasticache.redis.commands_latency.key_based<br>elasticache.redis.commands_latency.string_based<br>elasticache.redis.commands_latency.hash_based<br>elasticache.redis.commands_latency.list_based<br>elasticache.redis.commands_latency.set_based<br>elasticache.redis.commands_latency.cluster_based<br>elasticache.redis.commands_latency.sorted_set_based<br>elasticache.redis.commands_latency.eval_based<br>elasticache.redis.commands_latency.geo_spatial_based<br>elasticache.redis.commands_latency.hyper_log_log_based<br>elasticache.redis.commands_latency.pub_sub_based<br>elasticache.redis.commands_latency.stream_based|float|Average|

### Valkey metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Commands|GetTypeCmds<br>SetTypeCmds<br>KeyBasedCmds<br>StringBasedCmds<br>HashBasedCmds<br>ListBasedCmds<br>SetBasedCmds<br>ClusterBasedCmds<br>SortedSetBasedCmds<br>EvalBasedCmds<br>GeoSpatialBasedCmds<br>HyperLogLogBasedCmds<br>PubSubBasedCmds<br>StreamBasedCmds<br>JsonBasedCmds<br>JsonBasedGetCmds<br>JsonBasedSetCmds<br>NonKeyTypeCmds|elasticache.valkey.commands.get_type<br>elasticache.valkey.commands.set_type<br>elasticache.valkey.commands.key_based<br>elasticache.valkey.commands.string_based<br>elasticache.valkey.commands.hash_based<br>elasticache.valkey.commands.list_based<br>elasticache.valkey.commands.set_based<br>elasticache.valkey.commands.cluster_based<br>elasticache.valkey.commands.sorted_set_based<br>elasticache.valkey.commands.eval_based<br>elasticache.valkey.commands.geo_spatial_based<br>elasticache.valkey.commands.hyper_log_log_based<br>elasticache.valkey.commands.pub_sub_based<br>elasticache.valkey.commands.stream_based<br>elasticache.valkey.commands.json_based<br>elasticache.valkey.commands.json_based_get<br>elasticache.valkey.commands.json_based_set<br>elasticache.valkey.commands.non_key_type|float|Average|
|Cache Hits/Misses|CacheHits<br>CacheMisses|elasticache.valkey.cache.cache_hits<br>elasticache.valkey.cache.cache_misses|float|Average|
|Evictions|Evictions|elasticache.valkey.evictions.evictions|float|Average|
|Error Count|ErrorCount|elasticache.valkey.errors.total|integer|Sum|
|IAM Authentication|IamAuthenticationExpirations<br>IamAuthenticationThrottling|elasticache.valkey.iam_authentication.expirations<br>elasticache.valkey.iam_authentication.throttling|integer|Sum|
|Failures|AuthenticationFailures<br>CommandAuthorizationFailures<br>KeyAuthorizationFailures<br>ChannelAuthorizationFailures|elasticache.valkey.failures.authentication<br>elasticache.valkey.failures.command_authorization<br>elasticache.valkey.failures.key_authorization<br>elasticache.valkey.failures.channel_authorization|integer|Sum|
|Memory|BytesUsedForCache|elasticache.valkey.memory.used_for_cache|bytes|Average|
|Valkey CPU|EngineCPUUtilization|elasticache.valkey.cpu.engine|percentage|Average|
|Disk IO Bytes|BytesReadFromDisk<br>BytesWrittenToDisk|elasticache.valkey.disk_io_bytes.read<br>elasticache.valkey.disk_io_bytes.write|bytes|Average|
|Disk IO Items|NumItemsReadFromDisk<br>NumItemsWrittenToDisk|elasticache.valkey.disk_io_items.read<br>elasticache.valkey.disk_io_items.write|float|Average|
|Is Primary|IsPrimary (IsMaster)|elasticache.valkey.is_primary.primary|integer|Average|
|Primary Link Health Status|PrimaryLinkHealthStatus (MasterLinkHealthStatus)|elasticache.valkey.primary_link_health_status.sync|integer|Average|
|Keys Tracked|KeysTracked|elasticache.valkey.keys_tracked.count|integer|Sum|
|Connections|CurrConnections<br>NewConnections|elasticache.valkey.connections.current<br>elasticache.valkey.connections.new|float|Average|
|Items|CurrItems<br>CurrVolatileItems<br>Reclaimed|elasticache.valkey.items.current<br>elasticache.valkey.items.current_volatile<br>elasticache.valkey.items.reclaimed|float|Average|
|Active Defrag|ActiveDefragHits|elasticache.valkey.active_defrag.hits|integer|Sum|
|Cache Hit Rate|CacheHitRate|elasticache.valkey.cache_hit_rate.hit|percentage|Average|
|Memory Percentage|DatabaseMemoryUsagePercentage<br>DatabaseMemoryUsageCountedForEvictPercentage|elasticache.valkey.memory_percentage.usage<br>elasticache.valkey.memory_percentage.counted_for_evict|percentage|Average|
|Capacity Percentage|DatabaseCapacityUsagePercentage<br>DatabaseCapacityUsageCountedForEvictPercentage|elasticache.valkey.capacity_percentage.usage<br>elasticache.valkey.capacity_percentage.counted_for_evict|percentage|Average|
|Average TTL|DB0AverageTTL|elasticache.valkey.average_ttl.db0|float|Average|
|Traffic Management|TrafficManagementActive|elasticache.valkey.traffic_management.active|integer|Average|
|Fragmentation Ratio|MemoryFragmentationRatio|elasticache.valkey.fragmentation_ratio.memory|float|Average|
|Replication Bytes|ReplicationBytes|elasticache.valkey.replication_bytes.replication|bytes|Average|
|Replication Lag|ReplicationLag|elasticache.valkey.replication_lag.lag|float|Average|
|Global Datastore Replication Lag|GlobalDatastoreReplicationLag|elasticache.valkey.global_replication_lag.lag|float|Average|
|Save In Progress|SaveInProgress|elasticache.valkey.save_in_progress.bgsave|integer|Maximum|
|Successful Request Latency|SuccessfulWriteRequestLatency<br>SuccessfulReadRequestLatency|elasticache.valkey.successful_request_latency.write<br>elasticache.valkey.successful_request_latency.read|float|Average|
|Commands Latency|GetTypeCmdsLatency<br>SetTypeCmdsLatency<br>KeyBasedCmdsLatency<br>StringBasedCmdsLatency<br>HashBasedCmdsLatency<br>ListBasedCmdsLatency<br>SetBasedCmdsLatency<br>ClusterBasedCmdsLatency<br>SortedSetBasedCmdsLatency<br>EvalBasedCmdsLatency<br>GeoSpatialBasedCmdsLatency<br>HyperLogLogBasedCmdsLatency<br>PubSubBasedCmdsLatency<br>StreamBasedCmdsLatency<br>JsonBasedCmdsLatency<br>JsonBasedGetCmdsLatency<br>JsonBasedSetCmdsLatency<br>NonKeyTypeCmdsLatency|elasticache.valkey.commands_latency.get_type<br>elasticache.valkey.commands_latency.set_type<br>elasticache.valkey.commands_latency.key_based<br>elasticache.valkey.commands_latency.string_based<br>elasticache.valkey.commands_latency.hash_based<br>elasticache.valkey.commands_latency.list_based<br>elasticache.valkey.commands_latency.set_based<br>elasticache.valkey.commands_latency.cluster_based<br>elasticache.valkey.commands_latency.sorted_set_based<br>elasticache.valkey.commands_latency.eval_based<br>elasticache.valkey.commands_latency.geo_spatial_based<br>elasticache.valkey.commands_latency.hyper_log_log_based<br>elasticache.valkey.commands_latency.pub_sub_based<br>elasticache.valkey.commands_latency.stream_based<br>elasticache.valkey.commands_latency.json_based<br>elasticache.valkey.commands_latency.json_based_get<br>elasticache.valkey.commands_latency.json_based_set<br>elasticache.valkey.commands_latency.non_key_type|float|Average|
<h2 id="notes">Precautions</h2>

- Amazon ElastiCache Serverless is not supported.
