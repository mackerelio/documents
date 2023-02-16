---
Title: Metric plugins - mackerel-plugin-jvm
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-jvm
---

mackerel-plugin-jvm retrieves various metrics about the JVM from commands such as jstat, jinfo and jps.

This plugin will not work if `PerfDisableSharedMem` is specified in the JVM options. For more information, see [here](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm#about-the-perfdisablesharedmem-jvm-option-issue) for details.

[:contents]

<h2 id="metrics">Monitorable metrics</h2>

The `XXX` in each graph name is set to the application name specified by the `--javaname` option.

### JVM XXX GC events

| Metric Display Name | Metric Name                | Diff | Stacked | Description                    |
| ------------------- | -------------------------- | ---- | ------- | ------------------------------ |
| Young GC event      | custom.jvm.#.gc_events.YGC | ✓    |         | Number of Young GC events      |
| Full GC event       | custom.jvm.#.gc_events.FGC | ✓    |         | Number of Full GC events       |
| Concurrent GC event | custom.jvm.#.gc_events.CGC | ✓    |         | Number of Concurrent GC events |

- Concurrent GC event is available in Java 11 or later.

### JVM XXX GC time (sec)

| Metric Display Name | Metric Name               | Diff | Stacked | Description                     |
| ------------------- | ------------------------- | ---- | ------- | ------------------------------- |
| Young GC time       | custom.jvm.#.gc_time.YGCT | ✓    |         | Execution time of Young GC      |
| Full GC time        | custom.jvm.#.gc_time.FGCT | ✓    |         | Execution time of Full GC       |
| Concurrent GC time  | custom.jvm.#.gc_time.CGCT | ✓    |         | Execution time of Concurrent GC |

- Concurrent GC time is available in Java 11 or later.

### JVM XXX GC time percentage

| Metric Display Name | Metric Name                          | Diff | Stacked | Description                                           |
| ------------------- | ------------------------------------ | ---- | ------- | ----------------------------------------------------- |
| Young GC time       | custom.jvm.#.gc_time_percentage.YGCT | ✓    |         | Percentage of Young GC in GC time per 60 seconds      |
| Full GC time        | custom.jvm.#.gc_time_percentage.FGCT | ✓    |         | Percentage of Full GC in GC time per 60 seconds       |
| Concurrent GC time  | custom.jvm.#.gc_time_percentage.CGCT | ✓    |         | Percentage of Concurrent GC in GC time per 60 seconds |

- Concurrent GC time is available in Java 11 or later.

### JVM XXX New Space memory

| Metric Display Name | Metric Name                  | Diff | Stacked | Description                             |
| ------------------- | ---------------------------- | ---- | ------- | --------------------------------------- |
| New max             | custom.jvm.#.new_space.NGCMX |      |         | Maximum capacity of New Generation (KB) |
| New current         | custom.jvm.#.new_space.NGC   |      |         | Current capacity of New Generation (KB) |
| Eden used           | custom.jvm.#.new_space.EU    |      |         | Eden space usage (KB)                   |
| Survivor0 used      | custom.jvm.#.new_space.S0U   |      |         | Current usage of Survivor space 0 (KB)  |
| Survivor1 used      | custom.jvm.#.new_space.S1U   |      |         | Current usage of Survivor space 1 (KB)  |


### JVM XXX Old Space memory

| Metric Display Name | Metric Name                  | Diff | Stacked | Description                                          |
| ------------------- | ---------------------------- | ---- | ------- | --------------------------------------- |
| Old max             | custom.jvm.#.old_space.OGCMX |      |         | Maximum capacity of Old Generation (KB) |
| Old current         | custom.jvm.#.old_space.OGC   |      |         | Current capacity of Old Generation (KB) |
| Old used            | custom.jvm.#.old_space.OU    |      |         | Old space usage (KB)                    |


### JVM XXX Permanent Space

| Metric Display Name | Metric Name                   | Diff | Stacked | Description                                   |
| ------------------- | ----------------------------- | ---- | ------- | --------------------------------------------- |
| Perm max            | custom.jvm.#.perm_space.PGCMX |      |         | Maximum capacity of Permanent Generation (KB) |
| Perm current        | custom.jvm.#.perm_space.PGC   |      |         | Current capacity of Permanent Generation (KB) |
| Perm used           | custom.jvm.#.perm_space.PU    |      |         | Permanent space usage (KB)                    |

- Java 8 or later is not supported.


### JVM XXX Metaspace

| Metric Display Name             | Metric Name                 | Diff | Stacked | Description                          |
| ------------------------------- | --------------------------- | ---- | ------- | ------------------------------------ |
| Metaspace capacity max          | custom.jvm.#.metaspace.MCMX |      |         | Maximum capacity of metaspace (KB)   |
| Metaspace capacity min          | custom.jvm.#.metaspace.MCMN |      |         | Minimum capacity of metaspace (KB)   |
| Metaspace capacity              | custom.jvm.#.metaspace.MC   |      |         | Metaspace capacity (KB)              |
| Metaspace utilization           | custom.jvm.#.metaspace.MU   |      |         | Metaspace usage (KB)                 |
| Compressed Class Space Capacity | custom.jvm.#.metaspace.CCSC |      |         | Compressed class space capacity (KB) |
| Compressed Class Space Used     | custom.jvm.#.metaspace.CCSU |      |         | Compressed class space used (KB)     |


### JVM XXX MemorySpace

| Metric Display Name               | Metric Name                                             | Diff | Stacked | Description                 |
| --------------------------------- | ------------------------------------------------------- | ---- | ------- | --------------------------- |
| GC Old Memory Space               | custom.jvm.#.memorySpace.oldSpaceRate                   |      |         | Old generation usage rate   |
| GC New Memory Space               | custom.jvm.#.memorySpace.newSpaceRate                   |      |         | New generation usage rate   |
| CMS Initiating Occupancy Fraction | custom.jvm.#.memorySpace.CMSInitiatingOccupancyFraction |      |         | Threshold to perform CMS GC |

- Old generation usage is calculated as `(Old used / Old current ) * 100`.
- New generation utilization is calculated as `(Survivor0 used + Survivor1 used + Eden used) / (Survivor0 current + Survivor1 current + Eden current) * 100`.
- If the `--remote` option is specified, the metric for CMS Initiating Occupancy Fraction is not retrieved.


<h2 id="options">Configurable options</h2>

The options that can be specified for the plug-ins are as follows.

| Option      | Description                                                               | Required | 初期値                                     |
| ----------- | ------------------------------------------------------------------------- | -------- | ------------------------------------------ |
| --host      | Deprecated: hostname of jps/jstat                                         |          |                                            |
| --port      | Deprecated: Target port of jps/jstat                                      |          | 0                                          |
| --remote    | Remote target for jps/jstat. Specified by `hostname[:port][/servername]`. |          |                                            |
| --jstatpath | Path to jstat command                                                     |          | `/usr/bin/jstat` or `$JAVA_HOME/bin/jstat` |
| --jinfopath | Path to jinfo command                                                     |          | `/usr/bin/jinfo` or `$JAVA_HOME/bin/jinfo` | 
| --jpspath   | Path to jps command                                                       |          | `/usr/bin/jps` or `$JAVA_HOME/bin/jps`     |
| --javaname  | The name of the application to be used as the graph name                  | ✓        |                                            |
| --pidfile   | Path to pidfile                                                           |          |                                            |
| --tempfile  | Path to Temp file                                                         |          |                                            |

- If the `--remote` option is specified, jps and jstat must be executable locally from this plugin.

<h2 id="config">Example configuration</h2>

```
[plugin.metrics.jvm-tomcat]
command = [ "mackerel-plugin-jvm", "--javaname", "tomcat", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo"]
```

If you have multiple JVMs running on the same host and wish to monitor each one individually, specify different values for `[plugin.metrics.xxxxxx]` as follows


```
[plugin.metrics.jvm-app01]
command = [ "mackerel-plugin-jvm", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo", "--javaname", "app01", "--pidfile", "/path/to/app01pidfile" ]

[plugin.metrics.jvm-app02]
command = [ "mackerel-plugin-jvm", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo", "--javaname", "app02", "--pidfile", "/path/to/app02pidfile" ]
```

<h2 id="troubleshoot">Troubleshooting</h2>

### `Failed to run exec jinfo. jinfo command timed out.` in mackerel-agent log

- It is thought that a timeout occurred when executing the jinfo command from the plugin, due to temporary host load or other factors.
- If the output is continuous, please run the jinfo command manually to check the status.
- It may be possible to avoid this problem by adjusting the timeout value (timeout_seconds) of the monitoring rule. Please refer to [Configuration](https://mackerel.io/ja/docs/entry/custom-checks#items) for details.


<h2 id="repository">Repository</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm

