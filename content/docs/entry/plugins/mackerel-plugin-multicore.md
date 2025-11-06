---
Title: Metric plugins - mackerel-plugin-multicore
Date: 2023-04-25T18:43:53+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-multicore
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889984439235
---

Mackerel-plugin-multicore is a plugin for monitoring multi-core CPUs. In the [system metrics](https://mackerel.io/docs/entry/spec/metrics), the cpu value is the sum of all cores. In contrast, this plugin posts usage per core as a metric.

[:contents]

<h2 id="metrics">Monitorable metrics</h2>

### MultiCore CPU

| Metric Display Name  | Metric Name                           | Diff  | Stacked | Description                                                      |
| --------- | -------------------------------- | --- | ------ | ------------------------------------------------------- |
| user      | custom.multicore.cpu.#.user      |    | ✓      | Time spent in user mode                                          |
| nice      | custom.multicore.cpu.#.nice      |    | ✓      | Time spent in user mode with low priority (nice)                            |
| system    | custom.multicore.cpu.#.system    |    | ✓      | Time spent in system mode                                          |
| idle      | custom.multicore.cpu.#.idle      |    | ✓      | Time spent in the idle task                                               |
| iowait    | custom.multicore.cpu.#.iowait    |    | ✓      | Time waiting for I/O to complete                                        |
| irq       | custom.multicore.cpu.#.irq       |    | ✓      | Time spent processing interrupts                                          |
| softirq   | custom.multicore.cpu.#.softirq   |    | ✓      | Time spent processing softirqs                                       |
| steal     | custom.multicore.cpu.#.steal     |    | ✓      | In a virtualized environment, time when the guest OS is not allocated CPU by the host OS or hypervisor |
| guest     | custom.multicore.cpu.#.guest     |    | ✓      | Time used by the virtual CPU of the guest OS                                           |
| guestNice | custom.multicore.cpu.#.guestNice |    | ✓      | Time used by the virtual CPU of the niced guest OS                               |

Post each value of cpu0 etc. in /proc/stat. # is replaced by cpu0, etc.

### MultiCore loadavg5 per core

| Metric Display Name  | Metric Name                           | Diff  | Stacked | Description |
| --- | --- | --- | --- | --- |
| loadavg5 | custom.multicore.loadavg_per_core.loadavg5 |  |  | The value of `loadavg5` divided by the number of CPU cores |

Post the value of loadavg5 in /proc/loadavg.


<h2 id="options">Configurable options</h2>

| Option | Short | Description | Default |
| --- | --- | --- | --- | 
| --tempfile |  | Specify the destination file path for tempfile |  |
| --help | -h | Show Help |  |

* The tempfile records the latest execution results
* By default, it is created under `/var/tmp/mackerel-agent/` as `mackerel-plugin-multicore`


<h2 id="config">Example configuration</h2>

```toml
[plugin.metrics.multicore]
command = ["mackerel-plugin-multicore"]
```

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-multicore
