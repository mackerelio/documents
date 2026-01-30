---
Title: Metric plugins - mackerel-plugin-jvm
Date: 2023-02-16T16:20:47+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-jvm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889963699303
---

mackerel-plugin-jvm retrieves various metrics about the JVM from commands such as jstat, jinfo and jps.

This plugin will not work if `PerfDisableSharedMem` is specified in the JVM options. For more information, see [here](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm#about-the-perfdisablesharedmem-jvm-option-issue) for details.

[:contents]

<h2 id="specification">Specification</h2>

Executes the following jstat command against the lvmid of the application specified by `--javaname`, or the process ID written in the pid file specified by `--pidfile`, and posts the output as metrics. The name after the last dot (`.`) in the metric name corresponds to each piece of information that can be retrieved with the jstat command.

- jstat -gc
- jstat -gccapacity
- jstat -gcnew
- jstat -gcold

<h2 id="metrics">Monitorable metrics</h2>

The <javaname\> in metric names and graph names is set to the application name specified by the `--javaname` option.

- If the `--metric-key` option is specified, the string specified in this option is used for the metric name.
- If the `--metric-label` option is specified, the string specified in this option is used for the graph name.

### JVM <javaname\> GC events

| Metric Display Name | Metric Name                | Diff | Stacked | Description                    |
| ------------------- | -------------------------- | ---- | ------- | ------------------------------ |
| Young GC event      | custom.jvm.<javaname\>.gc_events.YGC | ✓    |         | Number of Young GC events      |
| Full GC event       | custom.jvm.<javaname\>.gc_events.FGC | ✓    |         | Number of Full GC events       |
| Concurrent GC event | custom.jvm.<javaname\>.gc_events.CGC | ✓    |         | Number of Concurrent GC events (Java 11 or later) |


### JVM <javaname\> GC time (sec)

| Metric Display Name | Metric Name               | Diff | Stacked | Description                     |
| ------------------- | ------------------------- | ---- | ------- | ------------------------------- |
| Young GC time       | custom.jvm.<javaname\>.gc_time.YGCT | ✓    |         | Execution time of Young GC      |
| Full GC time        | custom.jvm.<javaname\>.gc_time.FGCT | ✓    |         | Execution time of Full GC       |
| Concurrent GC time  | custom.jvm.<javaname\>.gc_time.CGCT | ✓    |         | Execution time of Concurrent GC (Java 11 or later) |


### JVM <javaname\> GC time percentage

| Metric Display Name | Metric Name                          | Diff | Stacked | Description                                           |
| ------------------- | ------------------------------------ | ---- | ------- | ----------------------------------------------------- |
| Young GC time       | custom.jvm.<javaname\>.gc_time_percentage.YGCT | ✓    |         | Percentage of Young GC in GC time per 60 seconds      |
| Full GC time        | custom.jvm.<javaname\>.gc_time_percentage.FGCT | ✓    |         | Percentage of Full GC in GC time per 60 seconds       |
| Concurrent GC time  | custom.jvm.<javaname\>.gc_time_percentage.CGCT | ✓    |         | Percentage of Concurrent GC in GC time per 60 seconds (Java 11 or later) |


### JVM <javaname\> New Space memory

| Metric Display Name | Metric Name                  | Diff | Stacked | Description                             |
| ------------------- | ---------------------------- | ---- | ------- | --------------------------------------- |
| New max             | custom.jvm.<javaname\>.new_space.NGCMX |      |         | Maximum capacity of New Generation (KB) |
| New current         | custom.jvm.<javaname\>.new_space.NGC   |      |         | Current capacity of New Generation (KB) |
| Eden used           | custom.jvm.<javaname\>.new_space.EU    |      |         | Eden space usage (KB)                   |
| Survivor0 used      | custom.jvm.<javaname\>.new_space.S0U   |      |         | Current usage of Survivor space 0 (KB)  |
| Survivor1 used      | custom.jvm.<javaname\>.new_space.S1U   |      |         | Current usage of Survivor space 1 (KB)  |


### JVM <javaname\> Old Space memory

| Metric Display Name | Metric Name                  | Diff | Stacked | Description                                          |
| ------------------- | ---------------------------- | ---- | ------- | --------------------------------------- |
| Old max             | custom.jvm.<javaname\>.old_space.OGCMX |      |         | Maximum capacity of Old Generation (KB) |
| Old current         | custom.jvm.<javaname\>.old_space.OGC   |      |         | Current capacity of Old Generation (KB) |
| Old used            | custom.jvm.<javaname\>.old_space.OU    |      |         | Old space usage (KB)                    |


### JVM <javaname\> Permanent Space

| Metric Display Name | Metric Name                   | Diff | Stacked | Description                                   |
| ------------------- | ----------------------------- | ---- | ------- | --------------------------------------------- |
| Perm max            | custom.jvm.<javaname\>.perm_space.PGCMX |      |         | Maximum capacity of Permanent Generation (KB) |
| Perm current        | custom.jvm.<javaname\>.perm_space.PGC   |      |         | Current capacity of Permanent Generation (KB) |
| Perm used           | custom.jvm.<javaname\>.perm_space.PU    |      |         | Permanent space usage (KB)                    |

- Java 8 or later is not supported.


### JVM <javaname\> Metaspace

| Metric Display Name             | Metric Name                 | Diff | Stacked | Description                          |
| ------------------------------- | --------------------------- | ---- | ------- | ------------------------------------ |
| Metaspace capacity max          | custom.jvm.<javaname\>.metaspace.MCMX |      |         | Maximum capacity of metaspace (KB)   |
| Metaspace capacity min          | custom.jvm.<javaname\>.metaspace.MCMN |      |         | Minimum capacity of metaspace (KB)   |
| Metaspace capacity              | custom.jvm.<javaname\>.metaspace.MC   |      |         | Metaspace capacity (KB)              |
| Metaspace utilization           | custom.jvm.<javaname\>.metaspace.MU   |      |         | Metaspace usage (KB)                 |
| Compressed Class Space Capacity | custom.jvm.<javaname\>.metaspace.CCSC |      |         | Compressed class space capacity (KB) |
| Compressed Class Space Used     | custom.jvm.<javaname\>.metaspace.CCSU |      |         | Compressed class space used (KB)     |


### JVM <javaname\> MemorySpace

| Metric Display Name               | Metric Name                                             | Diff | Stacked | Description                 |
| --------------------------------- | ------------------------------------------------------- | ---- | ------- | --------------------------- |
| GC Old Memory Space               | custom.jvm.<javaname\>.memorySpace.oldSpaceRate                   |      |         | Old generation usage rate   |
| GC New Memory Space               | custom.jvm.<javaname\>.memorySpace.newSpaceRate                   |      |         | New generation usage rate   |
| CMS Initiating Occupancy Fraction | custom.jvm.<javaname\>.memorySpace.CMSInitiatingOccupancyFraction |      |         | Threshold to perform CMS GC |

- GC Old Memory Space is calculated as `(Old used / Old current ) * 100`.
- GC New Memory Space is calculated as `(Survivor0 used + Survivor1 used + Eden used) / (Survivor0 current + Survivor1 current + Eden current) * 100`.
- CMS Initiating Occupancy Fraction is not posted when the `--remote` option is specified.
  - If the `--remote` option is not specified, the following command will be executed when retrieving information.
    - jinfo -flag UseConcMarkSweepGC
    - jinfo -flag CMSInitiatingOccupancyFraction


<h2 id="options">Configurable options</h2>

The options that can be specified for the plug-ins are as follows.

| Option      | Description                                                               | Required | Default                                    |
| ----------- | ------------------------------------------------------------------------- | -------- | ------------------------------------------ |
| --host      | Deprecated: hostname of jps/jstat                                         |          |                                            |
| --port      | Deprecated: Target port of jps/jstat                                      |          | 0                                          |
| --remote    | Remote target for jps/jstat. Specified by `hostname[:port][/servername]`. |          |                                            |
| --jstatpath | Path to jstat command                                                     |          | `/usr/bin/jstat` or `$JAVA_HOME/bin/jstat` |
| --jinfopath | Path to jinfo command                                                     |          | `/usr/bin/jinfo` or `$JAVA_HOME/bin/jinfo` | 
| --jpspath   | Path to jps command                                                       |          | `/usr/bin/jps` or `$JAVA_HOME/bin/jps`     |
| --javaname  | The name of the application to be used as the graph name                  | ✓        |                                            |
| --pidfile   | Path to pidfile                                                           |          |                                            |
| --metric-key | String to be used in the metric name |          | Application name specified in --javaname |
| --metric-label | String to be used in the graph name |          | Application name specified in --javaname |
| --tempfile  | Path to Temp file                                                         |          |                                            |

- If the `--remote` option is specified, jps and jstat must be executable locally from this plugin.
- If there is more than one application with the name specified in the `--javaname` option, only one of the metrics will be posted.
  - When monitoring multiple applications with duplicate names on the same host, write a plugin configuration for each application and use the `--pidfile` option to specify the pid file of the application to be monitored. Also, specify a different name for each application in `--javaname` or `--metric-key` and `--metric-label` (see [Example](#config) for reference).

<h2 id="config">Example configuration</h2>

```toml
[plugin.metrics.jvm-tomcat]
command = [ "mackerel-plugin-jvm", "--javaname", "tomcat", "--jstatpath", "/usr/bin/jstat", "--jpspath", "/usr/bin/jps", "--jinfopath", "/usr/bin/jinfo"]
```

When monitoring multiple applications with duplicate names on the same host, configure as follows.

```toml
[plugin.metrics.app01]
command = ["mackerel-plugin-jvm", "--javaname", "app", "--pidfile", "/path/to/app01/app.pid", "--metric-key", "app01", "--metric-label", "app01"]

[plugin.metrics.app02]
command = ["mackerel-plugin-jvm", "--javaname", "app", "--pidfile", "/path/to/app02/app.pid", "--metric-key", "app02", "--metric-label", "app02"]
```

<h2 id="troubleshoot">Troubleshooting</h2>

### `Failed to run exec jinfo. jinfo command timed out.` in mackerel-agent log

- It is thought that a timeout occurred when executing the jinfo command from the plugin, due to temporary host load or other factors.
- If the output is continuous, please run the jinfo command manually to check the status.
- It may be possible to avoid this problem by adjusting the timeout value (timeout_seconds) of the monitoring rule. Please refer to [Configuration](https://mackerel.io/docs/entry/custom-checks#items) for details.


<h2 id="repository">Repository</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-jvm
