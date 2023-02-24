---
Title: Metrics specifications
Date: 2015-02-16T16:55:58+09:00
URL: https://mackerel.io/docs/entry/spec/metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083896568
---

This section describes the types of metrics displayed on the Host Details and Service Details screens.

[:contents]

<h2 id="system-metric">System metrics</h2>

System metrics are a set of metrics that are automatically posted to the host when mackerel-agent is installed.

### Linux

There are six types of graphs which will be displayed: loadavg, cpu, memory, disk, interface, and filesystem. The following are the metric values which will be graphed by each.

* loadavg (line graph)
  * `loadavg1`
  * `loadavg5`
  * `loadavg15`
* cpu (stacked graph)
  * `cpu.user.percentage`
  * `cpu.iowait.percentage`
  * `cpu.system.percentage`
  * `cpu.idle.percentage`
  * `cpu.nice.percentage`
  * `cpu.irq.percentage`
  * `cpu.softirq.percentage`
  * `cpu.steal.percentage`
  * `cpu.guest.percentage`
* memory (stacked graph. However, `total`, `swap_total`, `swap_used`, and `swap_cached` are line graphs)
  * Kernel 3.14 or later for Linux
      * `memory.used` (Value calculated by total - available)
      * `memory.available`
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
  * Other Linux
      * `memory.free`
      * `memory.buffers`
      * `memory.cached`
      * `memory.used` (Value calculated by total - free - buffers - cached)
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
* disk (line graph)
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface (line graph)
  * `interface.*.rxBytes.delta` (Number of bytes received)
  * `interface.*.txBytes.delta` (Number of bytes sent)
* filesystem (line graph)
  * `filesystem.*.size`
  * `filesystem.*.used`

The sources for each of the above metrics when using mackerel-agent are as follows. The source code of mackerel-agent is available [here](https://github.com/mackerelio/mackerel-agent).

* loadavg
  * Obtained by parsing the contents of `/proc/loadavg`
* cpu
  * Obtained by parsing the contents of `/proc/stat`
* memory
  * Obtained by parsing the contents of `/proc/meminfo`
* disk
  * Obtained by parsing the contents of `/proc/diskstats`
* interface
  * Obtained by parsing the contents of `/proc/net/dev`
* filesystem
  * Obtained by parsing the execution results of the `df` command

### Windows

There are six types of graphs which will be displayed: processor queue length, cpu, memory, disk, interface, and filesystem. The following are the metric values which will be graphed by each.

* processor queue length (line graph)
  * `processor_queue_length`
* cpu (stacked graph)
  * `cpu.user.percentage`
  * `cpu.system.percentage`
  * `cpu.idle.percentage`
* memory (stacked graph. However, `total`, `pagefile_free`, and `pagefile_total` are line graphs)
  * `memory.free`
  * `memory.used`
  * `memory.total`
  * `memory.pagefile_free`
  * `memory.pagefile_total`
* disk(line graph)
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface(line graph)
  * `interface.*.rxBytes.delta` (Number of bytes received)
  * `interface.*.txBytes.delta` (Number of bytes sent)
* filesystem(line graph)
  * `filesystem.*.size`
  * `filesystem.*.used`

The sources for each of the above metrics when using mackerel-agent are as follows. The source code of mackerel-agent is available [here](https://github.com/mackerelio/mackerel-agent).

* processor queue length
  * Information is obtained with the Windows Performance Data Helper API
* cpu
  * Information is obtained with the Windows Performance Data Helper API
* memory
  * Obtained from the results of the Win32 API `GlobalMemoryStatusEx`
* disk
  * Information is obtained from the `Win32_PerfFormattedData_PerfDisk_LogicalDisk` class using WMI
* interface
  * Information is obtained with the Windows Performance Data Helper API
* filesystem
  * Obtained and calculated from the results of the following Win32 API functions
      * `GetLogicalDriveStringsW`
      * `QueryDosDeviceW`
      * `GetVolumeInformationW`
      * `GetDiskFreeSpaceExW`

<h2 id="user-defined-metric">Custom metrics</h2>

Custom metrics are non-system metrics that users can arbitrarily submit to the host via plugins or the Mackerel API.

* For example, `custom.foo`, `custom.foo.bar`
  * Metrics that have up to the last dot in the metric name in common are posted to a single graph.
  * Metric names must be prefixed; custom metrics submitted by mackerel-agent will automatically be prefixed with `custom.`. (mackerel-agent version 0.6.1 or later)
  * For detailed specifications of custom metric names, see [here](https://mackerel.io/api-docs/entry/host-metrics#post-graphdef).

See below for instructions on how to submit custom metrics.

* [Using the official plugin pack to visualize middleware metrics](https://mackerel.io/docs/entry/howto/mackerel-agent-plugins)
* [Posting user-defined custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
* [Post user-defined custom metrics using the Mackerel API](https://mackerel.io/api-docs/entry/host-metrics#post)

<h2 id="service-metric">Service metrics</h2>

A service metric is a metric that is tied to a service rather than a specific host. Response time, which is visualized when [External URL Monitoring](https://mackerel.io/docs/entry/external-monitoring) is tied to a service, is also posted as a service metric. Graphs of service metrics can be viewed on the detail screen for [each service](https://mackerel.io/my/services).

See Posting Service Metrics in the [Mackerel API Documents](https://mackerel.io/api-docs/) for information on how to [Post Service Metrics](https://mackerel.io/api-docs/entry/service-metrics#post).
