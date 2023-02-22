---
Title: メトリックプラグイン - mackerel-plugin-jvm
Date: 2023-02-16T16:21:09+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-jvm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889963699440
CustomPath: plugins/mackerel-plugin-jvm
---

mackerel-plugin-jvm は jstat/jinfo/jps などのコマンドからJVMに関する各種メトリックを取得します。

本プラグインはJVMオプションに`PerfDisableSharedMem`が指定されている場合に動作しません。詳しくは[こちら](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm#about-the-perfdisablesharedmem-jvm-option-issue)をご確認ください。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

各グラフ名の`XXX`には`--javaname`オプションで指定したアプリケーション名が設定されます。

### JVM XXX GC events

| メトリック表示名    | メトリック名               | 差分 | 積み上げ表示 | 説明                              |
| ------------------- | -------------------------- | ---- | ------------ | --------------------------------- |
| Young GC event      | custom.jvm.#.gc_events.YGC | ◯    | -            | Young GCの回数                    |
| Full GC event       | custom.jvm.#.gc_events.FGC | ◯    | -            | Full GCの回数                     |
| Concurrent GC event | custom.jvm.#.gc_events.CGC | ◯    | -            | Concurrent GCの回数（Java11以降） |


### JVM XXX GC time (sec)

| メトリック表示名   | メトリック名              | 差分 | 積み上げ表示 | 説明                                  |
| ------------------ | ------------------------- | ---- | ------------ | ------------------------------------- |
| Young GC time      | custom.jvm.#.gc_time.YGCT | ◯    | -            | Young GCの実行時間                    |
| Full GC time       | custom.jvm.#.gc_time.FGCT | ◯    | -            | Full GCの実行時間                     |
| Concurrent GC time | custom.jvm.#.gc_time.CGCT | ◯    | -            | Concurrent GCの実行時間（Java11以降） |


### JVM XXX GC time percentage

| メトリック表示名   | メトリック名                         | 差分 | 積み上げ表示 | 説明                                                        |
| ------------------ | ------------------------------------ | ---- | ------------ | ----------------------------------------------------------- |
| Young GC time      | custom.jvm.#.gc_time_percentage.YGCT | ◯    | -            | Young GCが60秒あたりのGC時間に占める割合                    |
| Full GC time       | custom.jvm.#.gc_time_percentage.FGCT | ◯    | -            | Full GCが60秒あたりのGC時間に占める割合                     |
| Concurrent GC time | custom.jvm.#.gc_time_percentage.CGCT | ◯    | -            | Concurrent GCが60秒あたりのGC時間に占める割合（Java11以降） |


### JVM XXX New Space memory

| メトリック表示名 | メトリック名                 | 差分 | 積み上げ表示 | 説明                          |
| ---------------- | ---------------------------- | ---- | ------------ | ----------------------------- |
| New max          | custom.jvm.#.new_space.NGCMX | -    | -            | New世代の最大容量(KB)         |
| New current      | custom.jvm.#.new_space.NGC   | -    | -            | New世代の現在の容量(KB)       |
| Eden used        | custom.jvm.#.new_space.EU    | -    | -            | Eden領域の使用量(KB)          |
| Survivor0 used   | custom.jvm.#.new_space.S0U   | -    | -            | Survivor領域0の現在の容量(KB) |
| Survivor1 used   | custom.jvm.#.new_space.S1U   | -    | -            | Survivor領域1の現在の容量(KB) |


### JVM XXX Old Space memory

| メトリック表示名 | メトリック名                 | 差分 | 積み上げ表示 | 説明                    |
| ---------------- | ---------------------------- | ---- | ------------ | ----------------------- |
| Old max          | custom.jvm.#.old_space.OGCMX | -    | -            | Old世代の最大容量(KB)   |
| Old current      | custom.jvm.#.old_space.OGC   | -    | -            | Old世代の現在の容量(KB) |
| Old used         | custom.jvm.#.old_space.OU    | -    | -            | Old領域の使用量(KB)     |


### JVM XXX Permanent Space

| メトリック表示名 | メトリック名                  | 差分 | 積み上げ表示 | 説明                            |
| ---------------- | ----------------------------- | ---- | ------------ | ------------------------------- |
| Perm max         | custom.jvm.#.perm_space.PGCMX | -    | -            | Permanent 世代の最大容量 (KB)   |
| Perm current     | custom.jvm.#.perm_space.PGC   | -    | -            | Permanent 世代の現在の容量 (KB) |
| Perm used        | custom.jvm.#.perm_space.PU    | -    | -            | Permanent 領域の使用量 (KB)     |

- Java 8 以降は非対応です。


### JVM XXX Metaspace

| メトリック表示名                | メトリック名                | 差分 | 積み上げ表示 | 説明                                   |
| ------------------------------- | --------------------------- | ---- | ------------ | -------------------------------------- |
| Metaspace capacity max          | custom.jvm.#.metaspace.MCMX | -    | -            | メタスペースの最大容量(KB)             |
| Metaspace capacity min          | custom.jvm.#.metaspace.MCMN | -    | -            | メタスペースの最小容量(KB)             |
| Metaspace capacity              | custom.jvm.#.metaspace.MC   | -    | -            | メタスペースの容量(KB)                 |
| Metaspace utilization           | custom.jvm.#.metaspace.MU   | -    | -            | メタスペースの使用量(KB)               |
| Compressed Class Space Capacity | custom.jvm.#.metaspace.CCSC | -    | -            | 圧縮されたクラス領域の容量(KB)         |
| Compressed Class Space Used     | custom.jvm.#.metaspace.CCSU | -    | -            | 使用されている圧縮されたクラス領域(KB) |


### JVM XXX MemorySpace

| メトリック表示名                  | メトリック名                                            | 差分 | 積み上げ表示 | 説明                 |
| --------------------------------- | ------------------------------------------------------- | ---- | ------------ | -------------------- |
| GC Old Memory Space               | custom.jvm.#.memorySpace.oldSpaceRate                   | -    | -            | Old世代の使用率      |
| GC New Memory Space               | custom.jvm.#.memorySpace.newSpaceRate                   | -    | -            | New世代の使用率      |
| CMS Initiating Occupancy Fraction | custom.jvm.#.memorySpace.CMSInitiatingOccupancyFraction | -    | -            | CMS GCを実行する閾値 |

- Old世代の使用率は`(Old used / Old current ) * 100`で算出しています。
- New世代の使用率は`(Survivor0 used + Survivor1 used + Eden used) / (Survivor0 current + Survivor1 current + Eden current) * 100`で算出しています。
- `--remote`オプションを指定する場合、CMS Initiating Occupancy Fractionに関するメトリックは取得されません。


<h2 id="options">指定可能なオプション</h2>

プラグインに指定可能なオプションは以下の通りです。

| オプション  | 説明                                                                  | 必須 | 初期値                                           |
| ----------- | --------------------------------------------------------------------- | ---- | ------------------------------------------------ |
| --host      | jps/jstat のターゲットホスト名（非推奨）                              |      |                                                  |
| --port      | jps/jstat のターゲットポート（非推奨）                                |      | 0                                                |
| --remote    | jps/jstat のリモートターゲット。 `hostname[:port][/servername]`で指定 |      |                                                  |
| --jstatpath | jstatコマンドのパス                                                   |      | `/usr/bin/jstat` もしくは `$JAVA_HOME/bin/jstat` |
| --jinfopath | jinfoコマンドのパス                                                   |      | `/usr/bin/jinfo` もしくは `$JAVA_HOME/bin/jinfo` |
| --jpspath   | jpsコマンドのパス                                                     |      | `/usr/bin/jps` もしくは `$JAVA_HOME/bin/jps`     |
| --javaname  | アプリケーション名。グラフ名として使用されます                        | ○    |                                                  |
| --pidfile   | pidファイルのパス                                                     |      |                                                  |
| --tempfile  | Tempファイル名                                                        |      |                                                  |

- `--remote`オプションを指定する場合、jps および jstat は、このプラグインからローカルで実行可能である必要があります。

<h2 id="config">エージェントへの設定例</h2>

```
[plugin.metrics.jvm-tomcat]
command = [ "mackerel-plugin-jvm", "--javaname", "tomcat", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo"]
```

同一ホスト上に複数のJVMが起動しており、それぞれを個別に監視したい場合には、`[plugin.metrics.xxxxx]`を以下のように指定します。

```
[plugin.metrics.jvm-app01]
command = [ "mackerel-plugin-jvm", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo", "--javaname", "app01", "--pidfile", "/path/to/app01pidfile" ]

[plugin.metrics.jvm-app02]
command = [ "mackerel-plugin-jvm", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo", "--javaname", "app02", "--pidfile", "/path/to/app02pidfile" ]
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### mackerel-agentのログに `Failed to run exec jinfo. jinfo command timed out.` と出力されている

- プラグインからjinfoのコマンドを実行する際に、一時的なホストの負荷などを要因としてタイムアウトが発生したものと考えられます。
- 継続的に出力されている場合、手動にてjinfoコマンドを実行して状況をご確認ください。
- 監視ルールのタイムアウト値（timeout_seconds）を調整することで回避できる場合もございます。[設定項目](https://mackerel.io/ja/docs/entry/custom-checks#items)を参考に調整してください。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm
