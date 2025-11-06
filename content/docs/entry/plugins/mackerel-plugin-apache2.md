---
Title: Metric plugins - mackerel-plugin-apache2
Date: 2022-12-15T16:26:11+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-apache2
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889945319728
---

mackerel-plugin-apache2 provides a way to monitor statistics about Apache2 as a metric.

To use this plugin, the Apache Status module (mod_status) must be enabled and accessible from the plugin.

[:contents]

<h2 id="metrics">Monitorable metrics</h2>

The metrics that can be monitored by mackerel-plugin-apache2 are as follows.

### Apache Workers

The number of running server processes and threads can be monitored.

| Metric Display Name | Metric Name                         | Diff | Stacked | Description                                |
| ------------------- | ----------------------------------- | ---- | ------- | ------------------------------------------ |
| Busy Workers        | custom.apache2.workers.busy_workers |      | ✓       | The number of busy worker requests/threads |
| Idle Workers        | custom.apache2.workers.idle_workers |      | ✓       | The number of idle worker requests/threads |

### Apache Bytes

The amount of data sent out by the httpd process can be monitored.

| Metric Display Name | Metric Name                     | Diff | Stacked | Description                    |
| ------------------- | ------------------------------- | ---- | ------- | ------------------------------ |
| Bytes Sent          | custom.apache2.bytes.bytes_sent | ✓    |         | Total traffic bytes per minute |

### Apache CPU Load

CPU utilization of the httpd process can be monitored.

| Metric Display Name | Metric Name                 | Diff | Stacked | Description     |
| ------------------- | --------------------------- | ---- | ------- | --------------- |
| CPU Load            | custom.apache2.cpu.cpu_load |      |         | CPU utilization |

### Apache Requests

The number of requests can be monitored.

| Metric Display Name | Metric Name                 | Diff | Stacked | Description                                |
| ------------------- | --------------------------- | ---- | ------- | ------------------------------------------ |
| Requests            | custom.apache2.req.requests | ✓    |         | The number of requests received per minute |

### Apache Scoreboard

Each metric on the scoreboard (state of running server processes) can be monitored.

| Metric Display Name    | Metric Name                       | Diff | Stacked | Description                                                               |
| ---------------------- | --------------------------------- | ---- | ------- | ------------------------------------------------------------------------- |
| Waiting for connection | custom.apache2.scoreboard.score-_ |      | ✓       | The number of processes/threads started and waiting to be accessed        |
| Starting up            | custom.apache2.scoreboard.score-S |      | ✓       | The number of processes/threads running                                   |
| Reading request        | custom.apache2.scoreboard.score-R |      | ✓       | The number of client requests being read                                  |
| Sending reply          | custom.apache2.scoreboard.score-W |      | ✓       | The number of responses to clients                                        |
| Keepalive              | custom.apache2.scoreboard.score-K |      | ✓       | The number of requests waiting for KeepAlive                              |
| DNS lookup             | custom.apache2.scoreboard.score-D |      | ✓       | The number of names resolved by DNS                                       |
| Closing connection     | custom.apache2.scoreboard.score-C |      | ✓       | The number of connections closed                                          |
| Logging                | custom.apache2.scoreboard.score-L |      | ✓       | The number of logs output                                                 |
| Gracefully finishing   | custom.apache2.scoreboard.score-G |      | ✓       | The number of cases waiting for the end of processing in graceful restart |
| Idle cleanup           | custom.apache2.scoreboard.score-I |      | ✓       | The number of processes/threads stopped                                   |
| Open slot              | custom.apache2.scoreboard.score-  |      | ✓       | The number of slots available                                             |


<h2 id="options">Configurable options</h2>

The options which can be configured in the plugin are as follows.

| Option                | Short  | Environment Variables   | Description                                                | Default             |
| --------------------- | ------ | ----------------------- | ---------------------------------------------------------- | ------------------- |
| --http_host           | -o     |                         | IP address being listened to by httpd                      | 127.0.0.1           |
| --http_port           | -p     |                         | Port number being listened to by httpd                     | 80                  |
| --header              | -H     |                         | HTTP header to be given at the time of request from plugin |                     |
| --status_page         | -s     |                         | URL path where statistics are available                    | /server-status?auto |
| --tempfile            | -t     | MACKEREL_PLUGIN_WORKDIR | Destination path for temporary file                        |                     |
| --metric-key-prefix   |        |                         | String to be given as prefix to the metric name            | apache2             |
| --metric-label-prefix |        |                         | String to be assigned to the graph name as prefix          | Apache              |

- Temporary files are created by default under `/var/tmp/mackerel-agent` or `/tmp` in the format `mackerel-plugin-{Prefix or other string}-{hash string}`.

<h2 id="config">Example configuration</h2>

```toml
[plugin.metrics.apache2]
command = ["mackerel-plugin-apache2", "-p", "1080"]
```

To check the above settings manually, you can execute the following at the terminal.

```
mackerel-plugin-apache2 -p 1080
```

<h2 id="config-apache">Configuration for Apache</h2>

Make sure that the Apache Status module (mod_status) is enabled in order to use mackerel-plugin-apache2.

The following configuration assumes publication at `http://127.0.0.1:1080/server-status`.

```
ExtendedStatus On
<VirtualHost 127.0.0.1:1080>
  <Location /server-status>
    SetHandler server-status
    Order deny,allow
    Deny from all
    Allow from localhost
  </Location>
</VirtualHost>
```

<h2 id="troubleshoot">Troubleshooting</h2>

### Cannot get metrics

If you are unable to obtain a metric after setting up the plugin, it may be due to a configuration that has not been reflected or an incorrect setting of the Status module on the Apache side. Please check the following points.

- Whether or not mackerel-agent has been restarted after the plugin has been configured.
  - Restarting mackerel-agent is required for the settings to take effect.
- Can you connect to the server-status endpoint with the curl command?
  - Please confirm that you can receive the HTTP 200 OK response with the following command when configured as shown in the configuration example.

    ```
    curl http://127.0.0.1:1080/server-status
    ```

  - If you have a `http` to `https` redirect configured in mod_rewrite, the plugin will not track the redirect; the Status module endpoint should be excluded from the redirect.
  - If you cannot get the results of the Status module with the curl command, it may be because it has been incorrectly configured or Apache has not been restarted.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-apache2

<h2 id="reference">References</h2>

- [httpd/include/scoreboard.h at trunk · apache/httpd · GitHub](https://github.com/apache/httpd/blob/trunk/include/scoreboard.h)

