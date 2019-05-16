---
Title: ' AWSインテグレーション - ElastiCache'
Date: 2016-12-28T16:32:55+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/elasticache
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687201753875
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/elasticache/" target="_blank">Amazon ElastiCache</a>のメトリック取得や監視に対応しています。
AWSインテグレーションで連携をおこなった場合、課金対象として 1ノード = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのElastiCache対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関してはAWSのヘルプ(<a href="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/CacheMetrics.HostLevel.html" target="_blank">ホストレベル</a>、<a href="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/mem-ug/CacheMetrics.Memcached.html" target="_blank">Memcached</a>、<a href="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/CacheMetrics.Redis.html" target="_blank">Redis</a>)をご確認ください。

Memcachedの場合は最大で39個、Redisの場合は最大で22個のメトリックが取得されます。

### ホストレベルメトリック
|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|CPU|CPUUtilization|elasticache.cpu.used|percentage|Average|
|Swap Usage|SwapUsage|elasticache.swap.usage|bytes|Average|
|Freeable Memory|FreeableMemory|elasticache.memory.freeable|bytes|Average|
|Network Traffic|NetworkBytesIn<br>NetworkBytesOut|elasticache.network.in<br>elasticache.network.out|bytes|Average|

### Memcachedメトリック
|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Commands|CmdGet<br>CmdSet<br>CmdFlush<br>CmdTouch<br>CmdConfigGet<br>CmdConfigSet|elasticache.memcached.commands.get<br>elasticache.memcached.commands.set<br>elasticache.memcached.commands.flush<br>elasticache.memcached.commands.touch<br>elasticache.memcached.commands.config_get<br>elasticache.memcached.commands.config_set|integer|Average|
|Cache Hits/Misses|GetHits<br>GetMisses<br>DeleteHits<br>DeleteMisses<br>IncrHits<br>IncrMisses<br>DecrHits<br>DecrMisses<br>CasBadval<br>CasHits<br>CasMisses<br>TouchHits<br>TouchMisses|elasticache.memcached.cache.get_hits<br>elasticache.memcached.cache.get_misses<br>elasticache.memcached.cache.delete_hits<br>elasticache.memcached.cache.delete_misses<br>elasticache.memcached.cache.incr_hits<br>elasticache.memcached.cache.incr_misses<br>elasticache.memcached.cache.decr_hits<br>elasticache.memcached.cache.decr_misses<br>elasticache.memcached.cache.cas_badval<br>elasticache.memcached.cache.cas_hits<br>elasticache.memcached.cache.cas_misses<br>elasticache.memcached.cache.touch_hits<br>elasticache.memcached.cache.touch_misses|integer|Average|
|Evictions|Evictions|elasticache.memcached.evictions.evictions|integer|Average|
|Unfetched|EvictedUnfetched<br>ExpiredUnfetched|elasticache.memcached.unfetched.evicted<br>elasticache.memcached.unfetched.expired|integer|Average|
|Traffics|BytesReadIntoMemcached<br>BytesWrittenOutFromMemcached|elasticache.memcached.traffics.bytes_read_into_memcached<br>elasticache.memcached.traffics.bytes_written_out_from_memcached|bytes|Average|
|Connections|CurrConnections<br>NewConnections|elasticache.memcached.connections.current<br>elasticache.memcached.connections.new|integer|Average|
|Items|CurrItems<br>NewItems<br>Reclaimed<br>CurrConfig<br>SlabsMoved|elasticache.memcached.items.current<br>elasticache.memcached.items.new<br>elasticache.memcached.items.reclaimed<br>elasticache.memcached.items.current_config<br>elasticache.memcached.items.slabs_moved|integer|Average|
|Memory|UnusedMemory<br>BytesUsedForHash<br>BytesUsedForCacheItems|elasticache.memcached.memory.unused<br>elasticache.memcached.memory.used_for_hash<br>elasticache.memcached.memory.used_for_cache_items|bytes|Average|

### Redisメトリック
|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Commands|GetTypeCmds<br>SetTypeCmds<br>KeyBasedCmds<br>StringBasedCmds<br>HashBasedCmds<br>ListBasedCmds<br>SetBasedCmds<br>SortedSetBasedCmds|elasticache.redis.commands.get_type<br>elasticache.redis.commands.set_type<br>elasticache.redis.commands.key_based<br>elasticache.redis.commands.string_based<br>elasticache.redis.commands.hash_based<br>elasticache.redis.commands.list_based<br>elasticache.redis.commands.set_based<br>elasticache.redis.commands.sorted_set_based|integer|Average|
|Cache Hits/Misses|CacheHits<br>CacheMisses|elasticache.redis.cache.cache_hits<br>elasticache.redis.cache.cache_misses|float|Average|
|Evictions|Evictions|elasticache.redis.evictions.evictions|integer|Average|
|Memory|BytesUsedForCache|elasticache.redis.memory.used_for_cache|bytes|Average|
|Redis CPU|EngineCPUUtilization|elasticache.redis.cpu.engine|percentage|Average|
|Connections|CurrConnections<br>NewConnections|elasticache.redis.connections.current<br>elasticache.redis.connections.new|integer|Average|
|Items|CurrItems<br>Reclaimed|elasticache.redis.items.current<br>elasticache.redis.items.reclaimed|integer|Average|
