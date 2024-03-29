---
Title: Metric plugins - mackerel-plugin-linux
Date: 2023-04-10T18:21:21+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-linux
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889979931357
CustomPath: plugins/mackerel-plugin-linux
---

mackerel-plugin-linux is a plugin that aggregates Linux information every minute and posts it as a metric.

[:contents]

<h2 id="metrics">Monitorable metrics</h2>

### Linux Users

| Metric Display Name | Metric Name            | Diff  | Stacked | Description         |
| -------- | ----------------- | --- | ---- | ---------- |
| Users    | linux.users.users |  |     | Number of logged-in users |

Post the number of users resulting from the who command.


### Linux Interrupts

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Interrupts | linux.interrupts.interrupts | ✓ || Number of interrupts generated by the system |

Post the value of intr in /proc/stat.


### Linux Context Switches

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Context Switches | linux.context_switches.context_switches | ✓ || Number of context switches |

Post the value of ctxt in /proc/stat.


### Linux Forks

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Forks | linux.forks.forks | ✓ || Number of forks |

Post the value of processes in /proc/stat.


### Disk Elapsed IO Time

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| <device\> IO Time | linux.disk.elapsed.iotime_<device\> | ✓ || Total time this block device has been active (ms) |
| <device\> IO Time Weighted | linux.disk.elapsed.iotime_weighted_<device\> | ✓ || Total waiting time for all requests (ms) |

Post io_ticks and time_in_queue values in /sys/block/<device\>/stat. <device\> is replaced by the device name.


### Disk Read/Write Time

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| <device\> Read | linux.disk.rwtime.tsreading_<device\> | ✓ || Total waiting time for read requests (ms) |
| <device\> Write | linux.disk.rwtime.tswriting_<device\> | ✓ || Total wait time for write requests (ms) |

Post values of read ticks and write ticks in /sys/block/<device\>/stat. <device\> is replaced by the device name.


### Linux Network Connection States

| Metric Display Name     | Metric Name              | Diff  | Stacked | Description            |
| -------- | ----------------- | --- | ---- | ---------- |
| Established  | linux.ss.ESTAB      |  | ✓      | Number of ESTAB      |
| Syn Sent     | linux.ss.SYN-SENT   |  | ✓      | Number of SYN-SENT   |
| Syn Received | linux.ss.SYN-RECV   |  | ✓      | Number of SYN-RECV   |
| Fin Wait 1   | linux.ss.FIN-WAIT-1 |  | ✓      | Number of FIN-WAIT-1 |
| Fin Wait 2   | linux.ss.FIN-WAIT-2 |  | ✓      | Number of FIN-WAIT-2 |
| Time Wait    | linux.ss.TIME-WAIT  |  | ✓      | Number of TIME-WAIT  |
| Close        | linux.ss.UNCONN     |  | ✓      | Number of UNCONN     |
| Close Wait   | linux.ss.CLOSE-WAIT |  | ✓      | Number of CLOSE-WAIT |
| Last Ack     | linux.ss.LAST-ACK   |  | ✓      | Number of LAST-ACK   |
| Listen       | linux.ss.LISTEN     |  | ✓      | Number of LISTEN     |
| Closing      | linux.ss.CLOSING    |  | ✓      | Number of CLOSING    |
| Unknown      | linux.ss.UNKNOWN    |  | ✓      | Number of UNKNOWN    |

Post the number of ss command results counted by State.


### Linux Swap Usage

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Swap In | linux.swap.pswpin | ✓ || Number of pages swap in |
| Swap Out | linux.swap.pswpout | ✓ || Number of pages swap out |

Post pswpin and pswpout values in /proc/vmstat.


<h2 id="options">Configurable options</h2>

| Option | Short | Description | Default |
| --- | --- | --- | --- | 
| --tempfile | -t | Specify the destination file path for tempfile |  |
| --type | -p | Filter the metrics to be retrieve<br>Choose all, users, proc_stat, diskstats, netstat, or swap | all |
| --version | -v | Show the plugin version |  |
| --help | -h | Show Help |  |

The tempfile contains the aggregated results. By default, it is created under `/var/tmp/mackerel-agent/` as `mackerel-plugin-linux-<hash string>`.

<h2 id="config">Example configuration</h2>

```
[plugin.metrics.linux]
command = ["mackerel-plugin-linux"]
```

<h2 id="repository">Repository</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-linux]
