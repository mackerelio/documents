---
Title: Monitoring Network Devices
Date: 2026-06-02T14:50:32+09:00
URL: https://mackerel.io/docs/entry/howto/network-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/14945776032036140075
---

With Mackerel, you can monitor your network in the following ways using official plugins and dedicated tools.

- Ping reachability to hosts and network devices
- Metrics retrieved from network devices via SNMP (such as interface traffic)

For monitoring a small number of hosts or devices, mackerel-agent combined with the official plugins is sufficient. To aggregate monitoring of a large number of network devices, you can use sabatrafficd / sabapingd offered through [Mackerel Labs](https://github.com/mackerelio-labs).

This page guides you through installing the plugins and dedicated tools and setting up simple configurations based on what you want to monitor. For detailed settings, please refer to the help documentation for the specific plugin or tool you use.

[:contents]

## Getting Started with Network Monitoring

### 1. Install the Official Plugin Packs

`check-ping`, which monitors ping responses, is bundled in the official check plugin pack. `mackerel-plugin-snmp`, which retrieves metrics via SNMP, is bundled in the official metric plugin pack. Depending on the monitoring you wish to use, refer to the following pages to install the relevant plugin pack.

- [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins)
- [Using the official plugin pack to visualize middleware metrics](https://mackerel.io/docs/entry/howto/mackerel-agent-plugins)

On Windows Server, these plugins are bundled with the Windows version of mackerel-agent.

### 2. Add Monitoring Configuration

`check-ping` sends ping requests from the server running the monitoring to the target host, while `mackerel-plugin-snmp` queries network devices from the server via SNMP. For either plugin, add the configuration corresponding to what you want to monitor to the end of the `mackerel-agent.conf` file on the server running the monitoring.

After appending the configuration, restart the mackerel-agent (or use `reload` on Linux) to start monitoring.

Below are configuration examples for different types of monitoring.

#### Monitoring ping responses (check-ping)

Using `check-ping`, you can monitor the ping reachability of a specified host or device.

The following example uses the monitor name `check-ping-sample` to send 5 packets to `192.168.1.1` and triggers a CRITICAL alert if none of them respond within 100 ms.

```toml
[plugin.checks.check-ping-sample]
command = ["check-ping", "-H", "192.168.1.1", "-n", "5", "-w", "100"]
```

<figure class="figure-image figure-image-fotolife" title="The host details screen showing a check monitor named check-ping-sample under Monitors"><div class="images-row mceNonEditable">[f:id:mackerelio:20260528170046p:plain][f:id:mackerelio:20260528170042p:plain]</div><figcaption>The host details screen showing a check monitor named check-ping-sample under Monitors</figcaption></figure>

Additionally, you can configure settings such as the response time threshold. For details, please refer to [Check plugins - check-ping - Mackerel Docs](https://mackerel.io/docs/entry/plugins/check-ping).

#### Retrieving metrics from network devices (mackerel-plugin-snmp)

Using `mackerel-plugin-snmp`, you can retrieve any numeric MIB information from SNMP-capable network devices such as switches and routers, and post it as metrics.

The following example queries the SNMP agent at `192.168.1.1` with the community name `public`, retrieves the OID `.1.3.6.1.2.1.2.2.1.10.1` (ifInOctets of the interface with index 1), and posts it as a counter difference under the metric name `ifInOctets`.

```toml
[plugin.metrics.snmp]
command = ["mackerel-plugin-snmp", "-name", "interface", "-host", "192.168.1.1", "-community", "public", ".1.3.6.1.2.1.2.2.1.10.1:ifInOctets:1:0:uint32"]
```

Additionally, you can configure settings such as the OID to retrieve, the graph name, and the unit. For details, please refer to [Metric plugins - mackerel-plugin-snmp - Mackerel Docs](https://mackerel.io/docs/entry/plugins/mackerel-plugin-snmp).

### 3. Modifying Check Monitoring Behavior

You can configure settings such as the check monitoring execution interval and the number of consecutive abnormal detections required to trigger an alert, as needed.
The following example changes the default 1-minute execution interval to 5 minutes and triggers an alert only if all results from three consecutive executions are abnormal.

```toml
[plugin.checks.check-ping-sample]
command = ["check-ping", "-H", "192.168.1.1", "-n", "5", "-w", "100"]
check_interval = 5m
max_check_attempts = 3
```

For other configurable items for check monitoring, refer to [Adding monitors for script checks - Mackerel Docs](https://mackerel.io/docs/entry/custom-checks#items).

## Monitoring a Large Number of Network Devices

Combining mackerel-agent with the official plugins requires per-device agent configuration, and as the number of devices grows, polling timing tends to concentrate and the setup becomes harder to scale. To efficiently monitor a large number of network devices, consider using sabatrafficd / sabapingd offered through Mackerel Labs.

Both sabatrafficd and sabapingd share the following characteristics:

- They are separate tools from mackerel-agent and run as resident processes on a monitoring server. They can coexist on the same host as mackerel-agent.
- You must create a host on Mackerel in advance for each network device to be monitored (described later).
- They distribute polling timing to scale to many devices.
- When the connection to the Mackerel posting endpoint is lost, they cache data in memory (and on disk, if configured), and resend it sequentially once the connection is restored.

Linux deb and rpm installation packages are available on the Releases page of each repository. Note that no Windows version is provided.

After installation, a configuration file template is created at `/etc/sabatrafficd/sabatrafficd.yaml` (or `/etc/sabapingd/sabapingd.yaml` for sabapingd). Edit it and then start the service, for example with `systemctl enable --now sabatrafficd`.

### Creating the destination host in advance

sabatrafficd / sabapingd post each device's metrics by associating them with a host on Mackerel. Create the destination host in advance using a tool such as [the mkr CLI tool](https://mackerel.io/docs/entry/advanced/cli), and specify its host ID or custom identifier in the configuration file.

```console
MACKEREL_APIKEY=<API_KEY> mkr create --customIdentifier <custom-identifier-name> <hostname>
```

### sabatrafficd (metric collection via SNMP)

[sabatrafficd](https://github.com/mackerelio-labs/sabatrafficd) is a tool that retrieves per-interface traffic volume, error packet counts, and similar information from network devices via SNMP, and posts the data to Mackerel as metrics.

Its main features are as follows:

- Interfaces (ports) are automatically detected. The following metrics are retrieved for each interface:
  - Traffic volume (`ifHCInOctets` / `ifHCOutOctets`)
  - Error packet counts (`ifInErrors` / `ifOutErrors`)
  - Discarded packet counts (`ifInDiscards` / `ifOutDiscards`)
- Traffic volume is posted as a system metric, so it is included in host connectivity monitoring.
- For device-specific MIBs such as CPU load or fan speed, you can specify the OID to post the value as an arbitrary custom metric.
- You can narrow down the target interfaces (include / exclude) and select SNMP v2c or v3.

An example configuration file (`config.yaml`) is shown below:

```yaml
x-api-key: XXXXX
collector:
  - host-id: xxxxx
    community: public
    host: 192.0.2.1
    mibs:
      - ifHCInOctets
      - ifHCOutOctets
      - ifInDiscards
      - ifOutDiscards
      - ifInErrors
      - ifOutErrors
    skip-linkdown: true
  - host-id: yyyyy
    community: public
    host: 192.0.2.2
    mibs:
      - ifHCInOctets
      - ifHCOutOctets
```

<figure class="figure-image figure-image-fotolife" title="Interface traffic graph of a network device collected by sabatrafficd">[f:id:mackerelio:20260528170438p:plain]<figcaption>Interface traffic graph of a network device collected by sabatrafficd</figcaption></figure>

For details, please refer to the README at [sabatrafficd - GitHub](https://github.com/mackerelio-labs/sabatrafficd).

### sabapingd (reachability monitoring via ICMP)

[sabapingd](https://github.com/mackerelio-labs/sabapingd) is a tool that pings target devices and posts the round-trip latency and packet loss to Mackerel as metrics.

Its main features are as follows:

- It converts the results of three ping attempts into metrics: latency (maximum / minimum / average) and packet loss. You can also configure it to post only the average latency.
- Since the data is posted as metrics, you can visualize trends and more easily estimate alert thresholds.

An example configuration file (`config.yaml`) is shown below:

```yaml
x-api-key: XXXXX
privileged: false
collector:
  - host-id: xxxxx
    host: 192.0.2.1
  - host-id: yyyyy
    host: 192.0.2.2
    always-send-packetloss: true
```

By default, the packet loss metric is not posted when its value is zero. To keep posting it continuously, use the `always-send-packetloss` option as shown in the example above.

<figure class="figure-image figure-image-fotolife" title="Latency graph of a network device collected by sabapingd">[f:id:mackerelio:20260528171109p:plain]<figcaption>Latency graph of a network device collected by sabapingd</figcaption></figure>

For details, please refer to the README at [sabapingd - GitHub](https://github.com/mackerelio-labs/sabapingd).
