---
Title: Check plugins - check-procs
Date: 2022-12-16T15:58:02+09:00
URL: https://mackerel.io/docs/entry/plugins/check-procs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889945587636
---

check-procs is a check plugin that monitors the number of processes. It counts the number of processes that match specified conditions and raises an alert if the number is less than or greater than a threshold.

[:contents]

<h2 id="options">Configurable options</h2>

| Option | Abbreviation | Description | Default |
| --- | --- | --- | --- |
| --warning-over | -w | Warning alert occurs when the number of processes matching the condition is greater than the specified value. |  |
| --critical-over | -c | Critical alert occurs when the number of processes matching the condition is greater than the specified value. |  |
| --warning-under | -W | Warning alert occurs when the number of processes matching the condition is less than the specified value. | 1 |
| --critical-under| -C | Critical alert occurs when the number of processes matching the condition is less than the specified value. | 1 |
| --match-self | -m | The process of executing check-procs itself is counted. |  |
| --match-parent | -M | Make the parent process of check-procs the target of the count. |  |
| --pattern | -p | Specify the process name pattern to be targeted by regular expression. |  |
| --exclude-pattern | -x | Specify the pattern of process names you want to exclude using regular expressions. |  |
| --ppid |  | Target processes matching the specified parent process ID. |  |
| --file-pid | -f | Target processes matching the specified process ID. |  |
| --virtual-memory-size | -z | Target processes whose virtual memory capacity is less than the specified value. |  |
| --resident-set-size | -r | Target processes whose physical memory capacity is less than the specified value. |  |
| --proportional-set-size | -P | Target processes whose CPU usage is less than or equal to the specified value. |  |
| --thread-count | -T | Target processes with less than or equal to the specified number of threads. |  |
| --state | -s | Target a process in a specified state. |  |
| --user | -u | Target processes running by the specified user. |  |
| --user-not | -U | Target processes not running by the specified user. |  |
| --esec-over | -e | Target processes whose elapsed time since the process was started exceeds the specified value. |  |
| --esec-under | -E | Target a process whose elapsed time since the process was started is less than the specified value. |  |
| --cpu-over | -i | Target processes whose CPU time used since the process was launched exceeds the specified value. |  |
| --cpu-under | -I | Target processes whose CPU time used since the process was launched is less than the specified value. |  |
| --help | -h | Show Help. |  |

If the option is not specified, all processes are counted.

<h3 id="options-parameter">Source of process information</h3>

The sources of process information that can be specified in the options are as follows.

- Linux
  - Result of ps command.
- Windows
  - The Win32_PerfFormattedData_PerfProc_Process class in WMI.

| Option | Linux | Windows |
| --- | --- | --- |
| --pattern / --exclude-pattern | COMMAND | Name |
| --user | USER | incompatible |
| --user-not | USER | incompatible |
| --ppid | PPID | incompatible |
| --file-pid | PID | IDProcess |
| --virtual-memory-size | VSZ | VirtualBytes |
| --resident-set-size | RSS | WorkingSet |
| --proportional-set-size | %CPU | PercentProcessorTime |
| --thread-count | NLWP | ThreadCount |
| --state | STATE | incompatible |
| --esec-over | ELAPSED | ElapsedTime |
| --esec-under | ELAPSED | ElapsedTime |
| --cpu-over | TIME | Always 0 |
| --cpu-under | TIME | Always 0 |


<h2 id="config">Example configurations</h2>

The configuration to generate an alert when none of the processes to be monitored exist is as follows.

```toml
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME"]
```

In the following example, if the number of monitored processes is less than 5, it becomes Warning, and if it is more than 10, it becomes Critical.

```toml
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-under", "5", "--critical-over", "10"]
```

<h2 id="tips">Tips</h2>

### Raise an alert if one or more processes are present

If you want to be alerted when one or more processes are present, but no processes are normal, specify 0 for each of `--warning-under` and `--critical-under`. Both options must be specified since the default value of these options is 1.

In the example below, the number of processes to be monitored is 0, 1 or more is Warning, and 2 or more is Critical.

```toml
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-under", "0", "--critical-under", "0", "--warning-over", "0", "--critical-over", "1"]
```

### Generate Warning alerts only

Specify 0 for `--critical-under` if you do not want Critical alerts to be generated when the number of monitored processes is less than the threshold.

In the example below, only a Warning alert is generated when none of the monitored processes exist.

```toml
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-under", "1", "--critical-under", "0"]
```

If you do not want a Critical alert to be generated when the number of monitored processes is greater than the threshold, specify a higher number for `--critical-over`.

In the example below, only Warning alerts are generated when there is more than one process to be monitored. The value of `--critical-over` should be adjusted according to your environment.

```toml
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-over", "0", "--critical-over", "9999"]
```

<h2 id="troubleshoot">Troubleshooting</h2>

### "Procs UNKNOWN: Exception thrown invalid class" occurs in Windows

This error occurs when information about WMI's Win32_PerfFormattedData_PerfProc_Process class cannot be obtained. Please execute the following command at the command prompt to isolate the cause.

`WMIC PATH Win32_PerfFormattedData_PerfProc_Process GET /FORMAT:LIST`

If it works properly, each item such as Name and IDProcess in the table listed as the [source of process information](#options-parameter) is output as standard output for each process.

If an error occurs, a WMI problem is suspected. Check the Event Viewer for WMI-related errors.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-procs
