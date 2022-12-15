---
Title: Metric plugins - mackerel-plugin-windows-server-sessions
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-windows-server-sessions
---

mackerel-plugin-windows-server-sessions can monitor the number of sessions on Windows shared folders as a metric.

[:contents]

<h2 id="metrics">Monitorable metrics</h2>

### Windows Server Sessions

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --- | --- | ---- | --- | --- |
| count | custom.windows.server.sessions.#.count |  |  | Number of shared folder sessions |

- The `#` in the metric name is replaced by the computer name.
- Post the number of sessions displayed by the `net session` command.
  - This is not the number of users logged in via remote desktop, etc.


<h2 id="config">Example configuration</h2>

```
[plugin.metrics.td-table-count]
command = ["mackerel-plugin-windows-server-sessions"]
```

<h2 id="troubleshoot">Troubleshooting</h2>

### Metric not posted

This plugin retrieves information from Win32_PerfFormattedData_PerfNet_Server class of WMI, Windows Management Instrumentation. If the metric is not posted, run the following command at the command prompt to isolate the cause.

`WMIC PATH Win32_PerfFormattedData_PerfNet_Server GET ServerSessions /FORMAT:CSV`

If it works properly, the output result is as follows.

```
Node,ServerSessions
<Computer Name>,0
```

If an error occurs, a problem on the WMI side is suspected. Check the Event Viewer for WMI-related errors.


<h2 id="repository">Repository</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-windows-server-sessions]
