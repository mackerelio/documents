---
Title: メトリックプラグイン - mackerel-plugin-jvm
Date: 2023-02-16T16:21:09+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-jvm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889963699440
---

mackerel-plugin-jvm は jstat/jinfo/jps などのコマンドからJVMに関する各種メトリックを取得します。

本プラグインはJVMオプションに`PerfDisableSharedMem`が指定されている場合に動作しません。詳しくは[こちら](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm#about-the-perfdisablesharedmem-jvm-option-issue)をご確認ください。

[:contents]

<h2 id="specification">仕様</h2>

`--javaname` に指定したアプリケーションの lvmid、もしくは `--pidfile` に指定した pid ファイルに書かれているプロセス ID に対して、以下の jstat コマンドを実行し、出力された結果をもとにメトリックとして投稿します。メトリック名の最後のドット（`.`）より後の名前が、jstat コマンドによって取得できる各情報に対応しています。

- jstat -gc
- jstat -gccapacity
- jstat -gcnew
- jstat -gcold

<h2 id="metrics">監視できるメトリック</h2>

メトリック名やグラフ名の<javaname\>には、`--javaname`オプションに指定したアプリケーション名が使用されます。

- `--metric-key`オプションの指定がある場合、メトリック名にはこちらに指定した文字列が使用されます。
- `--metric-label`オプションの指定がある場合、グラフ名にはこちらに指定した文字列が使用されます。

### JVM <javaname\> GC events

| メトリック表示名         | メトリック名                    | 差分 | 積み上げ表示 | 説明                           |
|---------------------|----------------------------|------|------------|------------------------------|
| Young GC event      | custom.jvm.<javaname\>.gc_events.YGC | ◯    | -          | Young GCの回数                  |
| Full GC event       | custom.jvm.<javaname\>.gc_events.FGC | ◯    | -          | Full GCの回数                   |
| Concurrent GC event | custom.jvm.<javaname\>.gc_events.CGC | ◯    | -          | Concurrent GCの回数（Java11以降） |


### JVM <javaname\> GC time (sec)

| メトリック表示名        | メトリック名                   | 差分 | 積み上げ表示 | 説明                               |
|--------------------|---------------------------|------|------------|----------------------------------|
| Young GC time      | custom.jvm.<javaname\>.gc_time.YGCT | ◯    | -          | Young GCの実行時間                  |
| Full GC time       | custom.jvm.<javaname\>.gc_time.FGCT | ◯    | -          | Full GCの実行時間                   |
| Concurrent GC time | custom.jvm.<javaname\>.gc_time.CGCT | ◯    | -          | Concurrent GCの実行時間（Java11以降） |


### JVM <javaname\> GC time percentage

| メトリック表示名        | メトリック名                              | 差分 | 積み上げ表示 | 説明                                              |
|--------------------|--------------------------------------|------|------------|-------------------------------------------------|
| Young GC time      | custom.jvm.<javaname\>.gc_time_percentage.YGCT | ◯    | -          | Young GCが60秒あたりのGC時間に占める割合                  |
| Full GC time       | custom.jvm.<javaname\>.gc_time_percentage.FGCT | ◯    | -          | Full GCが60秒あたりのGC時間に占める割合                   |
| Concurrent GC time | custom.jvm.<javaname\>.gc_time_percentage.CGCT | ◯    | -          | Concurrent GCが60秒あたりのGC時間に占める割合（Java11以降） |


### JVM <javaname\> New Space memory

| メトリック表示名    | メトリック名                      | 差分 | 積み上げ表示 | 説明                        |
|----------------|------------------------------|------|------------|---------------------------|
| New max        | custom.jvm.<javaname\>.new_space.NGCMX | -    | -          | New世代の最大容量（KB）        |
| New current    | custom.jvm.<javaname\>.new_space.NGC   | -    | -          | New世代の現在の容量（KB）      |
| Eden used      | custom.jvm.<javaname\>.new_space.EU    | -    | -          | Eden領域の使用量（KB）         |
| Survivor0 used | custom.jvm.<javaname\>.new_space.S0U   | -    | -          | Survivor領域0の現在の容量（B） |
| Survivor1 used | custom.jvm.<javaname\>.new_space.S1U   | -    | -          | Survivor領域1の現在の容量（KB） |


### JVM <javaname\> Old Space memory

| メトリック表示名 | メトリック名                      | 差分 | 積み上げ表示 | 説明                  |
|-------------|------------------------------|------|------------|---------------------|
| Old max     | custom.jvm.<javaname\>.old_space.OGCMX | -    | -          | Old世代の最大容量（KB）  |
| Old current | custom.jvm.<javaname\>.old_space.OGC   | -    | -          | Old世代の現在の容量（KB） |
| Old used    | custom.jvm.<javaname\>.old_space.OU    | -    | -          | Old領域の使用量（KB）    |


### JVM <javaname\> Permanent Space

| メトリック表示名  | メトリック名                       | 差分 | 積み上げ表示 | 説明                          |
|--------------|-------------------------------|------|------------|-----------------------------|
| Perm max     | custom.jvm.<javaname\>.perm_space.PGCMX | -    | -          | Permanent世代の最大容量 （KB）  |
| Perm current | custom.jvm.<javaname\>.perm_space.PGC   | -    | -          | Permanent世代の現在の容量 （KB） |
| Perm used    | custom.jvm.<javaname\>.perm_space.PU    | -    | -          | Permanent領域の使用量 （KB）    |

- Java 8 以降は非対応です。


### JVM <javaname\> Metaspace

| メトリック表示名                     | メトリック名                     | 差分 | 積み上げ表示 | 説明                        |
|---------------------------------|-----------------------------|------|------------|---------------------------|
| Metaspace capacity max          | custom.jvm.<javaname\>.metaspace.MCMX | -    | -          | メタスペースの最大容量（KB）         |
| Metaspace capacity min          | custom.jvm.<javaname\>.metaspace.MCMN | -    | -          | メタスペースの最小容量（KB）         |
| Metaspace capacity              | custom.jvm.<javaname\>.metaspace.MC   | -    | -          | メタスペースの容量（KB）             |
| Metaspace utilization           | custom.jvm.<javaname\>.metaspace.MU   | -    | -          | メタスペースの使用量（KB）           |
| Compressed Class Space Capacity | custom.jvm.<javaname\>.metaspace.CCSC | -    | -          | 圧縮されたクラス領域の容量（KB）     |
| Compressed Class Space Used     | custom.jvm.<javaname\>.metaspace.CCSU | -    | -          | 使用されている圧縮されたクラス領域（KB） |


### JVM <javaname\> MemorySpace

| メトリック表示名                       | メトリック名                                                 | 差分 | 積み上げ表示 | 説明              |
|-----------------------------------|---------------------------------------------------------|------|------------|-----------------|
| GC Old Memory Space               | custom.jvm.<javaname\>.memorySpace.oldSpaceRate                   | -    | -          | Old世代の使用率    |
| GC New Memory Space               | custom.jvm.<javaname\>.memorySpace.newSpaceRate                   | -    | -          | New世代の使用率    |
| CMS Initiating Occupancy Fraction | custom.jvm.<javaname\>.memorySpace.CMSInitiatingOccupancyFraction | -    | -          | CMS GCを実行する閾値 |

- GC Old Memory Space は `(Old used / Old current ) * 100` で算出しています。
- GC New Memory Space は `(Survivor0 used + Survivor1 used + Eden used) / (Survivor0 current + Survivor1 current + Eden current) * 100` で算出しています。
- CMS Initiating Occupancy Fraction は `--remote` オプションを指定した場合に投稿されません。
  - `--remote` オプションの指定がない場合は、情報取得の際に以下のコマンドが実行されます。
    - jinfo -flag UseConcMarkSweepGC
    - jinfo -flag CMSInitiatingOccupancyFraction

<h2 id="options">指定可能なオプション</h2>

プラグインに指定可能なオプションは以下の通りです。

| オプション          | 説明                                                      | 必須 | 初期値                                       |
|----------------|-----------------------------------------------------------|------|----------------------------------------------|
| --host         | jps/jstat のターゲットホスト名（非推奨）                             |      |                                              |
| --port         | jps/jstat のターゲットポート（非推奨）                               |      | 0                                            |
| --remote       | jps/jstat のリモートターゲット。 `hostname[:port][/servername]`で指定 |      |                                              |
| --jstatpath    | jstatコマンドのパス                                              |      | `/usr/bin/jstat` もしくは `$JAVA_HOME/bin/jstat` |
| --jinfopath    | jinfoコマンドのパス                                              |      | `/usr/bin/jinfo` もしくは `$JAVA_HOME/bin/jinfo` |
| --jpspath      | jpsコマンドのパス                                                |      | `/usr/bin/jps` もしくは `$JAVA_HOME/bin/jps`     |
| --javaname     | 監視対象のアプリケーション名                                       | ○    |                                              |
| --pidfile      | pidファイルのパス                                                |      |                                              |
| --metric-key   | メトリック名に使用する文字列                                      |      | --javaname に指定したアプリケーション名                 |
| --metric-label | グラフ名に使用する文字列列                                      |      | --javaname に指定したアプリケーション名                 |
| --tempfile     | Tempファイル名                                                |      |                                              |

- `--remote` オプションを指定する場合、jps および jstat は、このプラグインからローカルで実行可能である必要があります。
- `--javaname` オプションに指定した名前のアプリケーションが複数存在する場合は、いずれか 1 つのメトリックのみ投稿されます。
  - 同一ホスト上の名前が重複する複数のアプリケーションを監視する場合は、アプリケーションごとにプラグインの設定を記述し、`--pidfile` オプションで監視対象にするアプリケーションの pid ファイルを指定してください。また、その際は `--javaname` または `--metric-key` と `--metric-label` に、アプリケーションごとに任意の異なる名前を指定してください（[エージェントへの設定例](#config) を参考にしてください）。

<h2 id="config">エージェントへの設定例</h2>

```
[plugin.metrics.jvm-tomcat]
command = [ "mackerel-plugin-jvm", "--javaname", "tomcat", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo"]
```

同一ホスト上の名前が重複する複数のアプリケーションを監視する場合は、以下のように設定します。

```
[plugin.metrics.app01]
command = ["mackerel-plugin-jvm", "--javaname", "app", "--pidfile", "/path/to/app01/app.pid", "--metric-key", "app01", "--metric-label", "app01"]

[plugin.metrics.app02]
command = ["mackerel-plugin-jvm", "--javaname", "app", "--pidfile", "/path/to/app02/app.pid", "--metric-key", "app02", "--metric-label", "app02"]
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### mackerel-agentのログに `Failed to run exec jinfo. jinfo command timed out.` と出力されている

- プラグインからjinfoのコマンドを実行する際に、一時的なホストの負荷などを要因としてタイムアウトが発生したものと考えられます。
- 継続的に出力されている場合、手動にてjinfoコマンドを実行して状況をご確認ください。
- 監視ルールのタイムアウト値（timeout_seconds）を調整することで回避できる場合もございます。[設定項目](https://mackerel.io/ja/docs/entry/custom-checks#items)を参考に調整してください。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm
