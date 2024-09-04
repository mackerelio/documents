---
Title: Metric plugins - mackerel-plugin-snmp
Date: 2022-11-21T18:10:43+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-snmp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889938595528
---

With mackerel-plugin-snmp it is possible to retrieve desired MIB data values from a network device via SNMP and monitor it as a metric.

This plugin supports SNMP v2c, but it does not support SNMP Trap.

[:contents]

<h2 id="metrics">Monitorable metrics</h2>

- It will post the desired MIB data values retrievable via SNMP v2c as metrics.

<h2 id="options">Configurable options</h2>

The options which can be configured in the plugin are as follows.

| Option     | Explanation                                                                                                                                                        | Default Value |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------- |
| -name      | Graph name                                                                                                                                                         | snmp          |
| -unit      | Units for the metric associated with the graph (Refer to `unit` [here] (https://mackerel.io/api-docs/entry/host-metrics#post-graphdef) for the possible units.)    | float         |
| -host      | Host which sends SNMP requests                                                                                                                                     | localhost     |
| -community | Community name                                                                                                                                                     | public        |
| -tempfile  | Temporary file save location                                                                                                                                       |               |

When defining the metric to be retrieved, use a colon (`:`) as a separator, e.g., `OID:NAME[:DIFF?][:STACK?][:COUNTER?]`. The items are as follows.

| Item  | Explanation                                                          | Required | Default Value |
| ----- | -------------------------------------------------------------------- | -------- | ------------- |
| OID   | OID of the MIB value to retrieve (Cannot be designated by MIB name)  | ✓        |               |
| NAME  | Metric Name                                                          | ✓        |               |
| DIFF  | Difference display as a counter value (Specify with "0:No or 1:Yes") |          | 0             |
| STACK | Stack display (Specify with "0:No or 1:Yes")                         |          | 0             |
| COUNTER | Specify the number of bits in the counter as uint32 or uint64      |          |               |

About specifying COUNTER

- If the counter being monitored overflows, the difference from the previous value cannot be calculated correctly, and the continuity of the graph drawing is lost, so it is recommended that you specify this, especially when monitoring a 32-bit counter.

<h2 id="config">Example configuration</h2>

In this plugin, the options must be configured before defining metrics.

The following is the configuration to post with `public` for the community, the OID `.1.2.3.4.5.6.7` for the MIB, `foo` for the graph name, `bar` for the metric name, and difference/stack display both set to "No".

```
[plugin.metrics.snmp]
command = ["mackerel-plugin-snmp", "-name", "foo", "-community", "public", ".1.2.3.4.5.6.7:bar:0:0"]
```

It is possible to post multiple metrics to the same graph by listing multiple metric definitions.

```
[plugin.metrics.snmp]
command = ["mackerel-plugin-snmp", "-name", "foo", "-community", "public", ".1.2.3.4.5.6.7:bar:0:0", ".1.2.3.4.5.6.8:fuga:0:0"]
```

To directly execute and confirm the above configuration via terminal, etc., please execute the following string.

```
mackerel-plugin-snmp -name foo -community public ".1.2.3.4.5.6.7:bar:0:0" ".1.2.3.4.5.6.8:fuga:0:0"
```

<h2 id="troubleshoot">Troubleshooting</h2>

### Metric cannot be retrieved

If a metric cannot be obtained, the designated OID may be incorrect, so please confirm it is possible to retrieve the OID normally via the snmpget command.

Furthermore, monitoring is not possible if the OID hierarchy contains a tree, so please try confirming in advance using the snmpget command rather than the snmpwalk command.

### Command timed out

It is possible that communication via SNMP is disabled, or the response is taking too long.
Please confirm it is possible to retrieve the OID normally via the snmpget command.

Furthermore, if there are errors in the configuration of the plugin options, it may not be able to communicate properly, resulting in a timeout.
Please refer to the [Example configuration](#Example configuration) and confirm that the options are configured correctly.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-snmp
