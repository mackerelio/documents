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

The metrics obtainable with AWS Integration’s ElastiCache support are as follows. For `Metric` explanations, refer to the AWS help pages (<a href="https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheMetrics.HostLevel.html" target="_blank">host level metrics</a>, <a href="https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/CacheMetrics.Memcached.html" target="_blank">Memcached</a>, and <a href="https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheMetrics.Redis.html" target="_blank">Redis</a>).

The maximum number of metrics obtainable is 39 with Memcached, and 22 with Redis.

### Host level metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|CPU|CPUUtilization|elasticache.cpu.used|percentage|Average|
|Swap Usage|SwapUsage|elasticache.swap.usage|bytes|Average|
|Freeable Memory|FreeableMemory|elasticache.memory.freeable|bytes|Average|
|Network Traffic|NetworkBytesIn<br>NetworkBytesOut|elasticache.network.in<br>elasticache.network.out|bytes|Average|

### Memcached metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Commands|CmdGet<br>CmdSet<br>CmdFlush<br>CmdTouch<br>CmdConfigGet<br>CmdConfigSet|elasticache.memcached.commands.get<br>elasticache.memcached.commands.set<br>elasticache.memcached.commands.flush<br>elasticache.memcached.commands.touch<br>elasticache.memcached.commands.config_get<br>elasticache.memcached.commands.config_set|integer|Average|
|Cache Hits/Misses|GetHits<br>GetMisses<br>DeleteHits<br>DeleteMisses<br>IncrHits<br>IncrMisses<br>DecrHits<br>DecrMisses<br>CasBadval<br>CasHits<br>CasMisses<br>TouchHits<br>TouchMisses|elasticache.memcached.cache.get_hits<br>elasticache.memcached.cache.get_misses<br>elasticache.memcached.cache.delete_hits<br>elasticache.memcached.cache.delete_misses<br>elasticache.memcached.cache.incr_hits<br>elasticache.memcached.cache.incr_misses<br>elasticache.memcached.cache.decr_hits<br>elasticache.memcached.cache.decr_misses<br>elasticache.memcached.cache.cas_badval<br>elasticache.memcached.cache.cas_hits<br>elasticache.memcached.cache.cas_misses<br>elasticache.memcached.cache.touch_hits<br>elasticache.memcached.cache.touch_misses|integer|Average|
|Evictions|Evictions|elasticache.memcached.evictions.evictions|integer|Average|
|Unfetched|EvictedUnfetched<br>ExpiredUnfetched|elasticache.memcached.unfetched.evicted<br>elasticache.memcached.unfetched.expired|integer|Average|
|Traffics|BytesReadIntoMemcached<br>BytesWrittenOutFromMemcached|elasticache.memcached.traffics.bytes_read_into_memcached<br>elasticache.memcached.traffics.bytes_written_out_from_memcached|bytes|Average|
|Connections|CurrConnections<br>NewConnections|elasticache.memcached.connections.current<br>elasticache.memcached.connections.new|integer|Average|
|Items|CurrItems<br>NewItems<br>Reclaimed<br>CurrConfig<br>SlabsMoved|elasticache.memcached.items.current<br>elasticache.memcached.items.new<br>elasticache.memcached.items.reclaimed<br>elasticache.memcached.items.current_config<br>elasticache.memcached.items.slabs_moved|integer|Average|
|Memory|UnusedMemory<br>BytesUsedForHash<br>BytesUsedForCacheItems|elasticache.memcached.memory.unused<br>elasticache.memcached.memory.used_for_hash<br>elasticache.memcached.memory.used_for_cache_items|bytes|Average|

### Redis metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Commands|GetTypeCmds<br>SetTypeCmds<br>KeyBasedCmds<br>StringBasedCmds<br>HashBasedCmds<br>ListBasedCmds<br>SetBasedCmds<br>SortedSetBasedCmds|elasticache.redis.commands.get_type<br>elasticache.redis.commands.set_type<br>elasticache.redis.commands.key_based<br>elasticache.redis.commands.string_based<br>elasticache.redis.commands.hash_based<br>elasticache.redis.commands.list_based<br>elasticache.redis.commands.set_based<br>elasticache.redis.commands.sorted_set_based|integer|Average|
|Cache Hits/Misses|CacheHits<br>CacheMisses|elasticache.redis.cache.cache_hits<br>elasticache.redis.cache.cache_misses|float|Average|
|Evictions|Evictions|elasticache.redis.evictions.evictions|integer|Average|
|Memory|BytesUsedForCache|elasticache.redis.memory.used_for_cache|bytes|Average|
|Redis CPU|EngineCPUUtilization|elasticache.redis.cpu.engine|percentage|Average|
|Connections|CurrConnections<br>NewConnections|elasticache.redis.connections.current<br>elasticache.redis.connections.new|integer|Average|
|Items|CurrItems<br>Reclaimed|elasticache.redis.items.current<br>elasticache.redis.items.reclaimed|integer|Average|
