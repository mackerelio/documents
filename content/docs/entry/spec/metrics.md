---
Title: Metrics specifications
Date: 2015-02-16T16:55:58+09:00
URL: https://mackerel.io/docs/entry/spec/metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083896568
---

This article is an explanation of the metrics which will be displayed in the host details screen as well as in the service details screen.

##System metrics
Metrics automatically collected / posted by installing / launching mackerel-agent.

### Linux

There are six types of graphs which will be displayed: loadavg, cpu, memory, disk, interface, and filesystem. The following are the metric values which will be graphed by each.

* loadavg(line graph)
  * `loadavg1`
  * `loadavg5`
  * `loadavg15`
* cpu(stacked graph)
  * `cpu.user.percentage`
  * `cpu.iowait.percentage`
  * `cpu.system.percentage`
  * `cpu.idle.percentage`
  * `cpu.nice.percentage`
  * `cpu.irq.percentage`
  * `cpu.softirq.percentage`
  * `cpu.steal.percentage`
  * `cpu.guest.percentage`
* memory(stacked graph. `memory.total`, `swap_total`, `swap_used`, and `swap_cached` are line graphs)
  * Kernel 3.14 or later for Linux
      * `memory.used`
      * `memory.available`
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
  * Other Linux
      * `memory.free`
      * `memory.buffers`
      * `memory.cached`
      * `memory.used`
      * `memory.total`
      * `memory.swap_used`
      * `memory.swap_cached`
      * `memory.swap_total`
* disk（line graph）
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface（line graph）
  * `interface.*.rxBytes.delta` (受信バイト数)
  * `interface.*.txBytes.delta` (送信バイト数)
* filesystem（line graph）
  * `filesystem.*.size`
  * `filesystem.*.used`

### Windows

There are six types of graphs which will be displayed: processor queue length, cpu, memory, disk, interface, and filesystem. The following are the metric values which will be graphed by each.

* processor queue length（line graph）
  * `processor_queue_length`
* cpu（stacked graph）
  * `cpu.user.percentage`
  * `cpu.system.percentage`
  * `cpu.idle.percentage`
* memory（stacked graph. `memory.total`, `pagefile_free`, and `pagefile_total` are line graphs）
  * `memory.free`
  * `memory.used`
  * `memory.total`
  * `memory.pagefile_free`
  * `memory.pagefile_total`
* disk(line graph)
  * `disk.*.reads.delta`
  * `disk.*.writes.delta`
* interface(line graph)
  * `interface.*.rxBytes.delta`　(the number of bytes received)
  * `interface.*.txBytes.delta`　(the number of bytes sent)
* filesystem(line graph)
  * `filesystem.*.size`
  * `filesystem.*.used`

<h2 id="user-defined-metric">User-defined metrics (Custom metrics)</h2>

* `custom.foo(.bar)*.*`
  * Metrics which have the same name up to the last dot in the name will be collected together and displayed in one graph.

Custom metrics sent from mackerel-agent are attached with the prefix `custom.` (from version 0.6.1). We recommend adding `custom.` even when using the API directly because standard metrics may now be added with the mackerel-agent update. See also [Posting user-defined custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics).
