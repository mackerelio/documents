---
Title: Check plugins - check-ping
Date: 2025-10-23T19:00:00+09:00
URL: https://mackerel.io/docs/entry/plugins/check-ping
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802888565297363153
---

check-ping is a plugin that monitors ping responses.

[:contents]

<h2 id="options">Configurable options</h2>

| Option      | Short  | Description                                                  | Default Value |
|-------------|--------|--------------------------------------------------------------|---------------|
| --host      | -H     | Hostname or IP address to monitor                           |               |
| --count     | -n     | Number of ping packets to send. If even one response is received within the threshold, the monitoring status becomes OK | 1             |
| --wait-time | -w     | Threshold for total time (RTT) from sending ping packets to receiving responses. Unit: milliseconds (ms) | 1000          |
| --status-as |        | Overwrite the monitoring status. For example, if you specify `CRITICAL=WARNING`, the monitoring status will become WARNING when it would be CRITICAL. Multiple values can be specified, separated by commas |               |
| --help      | -h     | Show help                                                    |               |

- `--status-as` is available in mackerel-check-plugins v0.50.1 and later.

<h2 id="config">Example configuration</h2>

The following configuration sends 5 packets to 192.168.1.1 and generates a Critical alert if none respond within 100ms.

```toml
[plugin.checks.check-ping-sample]
command = ["check-ping", "-H", "192.168.1.1", "-n", "5", "-w", "100"]
```

To test the configuration, run the plugin directly in a terminal as follows:

```bash
check-ping -H 192.168.1.1 -n 5 -w 100
```

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-ping
