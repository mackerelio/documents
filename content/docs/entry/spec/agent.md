---
Title: mackerel-agent specifications
Date: 2015-04-20T15:59:08+09:00
URL: https://mackerel.io/docs/entry/spec/agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450092082290
---

This article is an explanation of the latest version of [mackerel-agent](https://mackerel.io/my/instruction-agent).



##Usage

```
mackerel-agent [-conf <config-file>] [options]
```

##Summary

Running in the foreground, `mackerel-agent`

- registers hosts information with Mackerel,
- gathers resource information and posts it to Mackerel (once every minute),
- updates hosts’ information (once every hour)

You can check your hosts’ and resources’ posted data on Mackerel’s web interface.

<h2 id="config-file">Configuration file</h2>

The configuration file is located here `/etc/mackerel-agent/mackerel-agent.conf`  by default. Description examples follow below.

```conf
apikey = "APIKEY"
pidfile = "/path/to/pidfile"
root = "/var/lib/mackerel-agent"

cloud_platform = "ec2"

verbose = false
silent = false

display_name = "My Host"

roles = [ "My-Service:app", "Another-Service:db" ]

http_proxy = "http://localhost:8080"

diagnostic = true

include = "/etc/mackerel-agent/conf.d/*.conf"

[host_status]
on_start = "working"
on_stop  = "poweroff"

[filesystems]
ignore = "/dev/ram.*"
use_mountpoint = true

[disks]
ignore = "loop"

[interfaces]
ignore = "bond.*"

[plugin.metrics.vmstat]
command = ["ruby", "/etc/sensu/plugins/system/vmstat-metrics.rb"]

[plugin.metrics.mysql]
command = ["ruby", "/path/to/your/plugin/mysql.rb"]
env = { MYSQL_USERNAME = "USERNAME", MYSQL_PASSWORD = "PASSWORD" }

[plugin.checks.heartbeat]
command = ["heartbeat.sh"]
env = { "ES_HOSTS" = "10.45.3.2:9220,10.45.3.1:9230" }
action = { command = "cardiac_massage", user = "doctor", env = { SAMPLE_KEY_1 = "VALUE_1", SAMPLE_KEY_2 = "VALUE_2" } }

[plugin.checks.ssh]
command = ["ruby", "/path/to/check-ssh.rb"]

[plugin.metadata.packages]
command = ["perl", "/path/to/packages.pl"]
```

<h3 id="config-file-format">About the configuration file format</h3>
The configuration file is in [TOML][] format. Therefore, the general description method, other than this page, will follow [TOML][] format.

<h3 id="config-file-encoding">About file encoding</h3>
If multibyte characters are included in the configuration, file encoding must be `UTF-8`.


<h3 id="config-file-editing">Points to note when editing the configuration file</h3>
The configuration file will be read when the agent starts up. Therefore, you'll have to restart the agent after changes are made to this file in order for them to be applied.


<h3>About configuration items</h3>
All options other than `apikey` are optional. Therefore, if the default settings are fine as is, it’s okay to leave values empty.

<h4 id="config-file-apikey">apikey</h4>
This specifies the API key that is used by the agent to communicate with the Mackerel service. A new API key can be issued or an existing API can be revoked from the [API Key Settings Tab] (https://mackerel.io/my?tab=apikeys).


<h4 id="config-file-pidfile">pidfile</h4>
Specifies the location to output the mackerel-agent pdfile. If not specified, it is output under `/var/run`.


<h4 id="config-file-root">root</h4>
Specifies the root directory of mackerel-agent. The file containing the host ID that is used for uniquely identifying the host in Mackerel is output in/to this directory.


<h4 id="config-file-cloudplatform">cloud_platform</h4>
When running mackerel-agent on Amazon EC2, Azure Virtual Machine, or Google Compute Engine, it automatically detects the IaaS environment. When one of these IaaS environments is identified, mackerel-agent collects the IaaS metadata and applies the necessary identifier (`custom_identifier`) to the host for integration with AWS integration or Azure integration.

Normally, this detection is done automatically, but you can specify the platform explicitly with the `cloud_platform` option in the configuration file. There are five valid values: `"auto"`(default) `"ec2"` `"gce"` `"azurevm"` `"none"`.

If you want to launch multiple mackerel-agents on one IaaS instance and for them be recognized as separate hosts, when using Docker container etc. for example, this option must be set to `"none"` in all of the configuration files except for the mackerel-agent running on the instance.


<h4 id="config-file-verbose">verbose</h4>
By setting `verbose = true`, the DEBUG log output can be enabled.


<h4 id="config-file-silent">silent</h4>
By setting `silent = true`, log output under WARN level can be suppressed.


<h4 id="config-file-displayname">display_name</h4>
An optional name can be set for a host by assigning a name within the configuration file. If a display name has already been assigned, the host will be updated to the new display name.

Even if the host name specification has been deleted from the configuration file, the display name will remain as it was configured. Display names can be deleted from the host's settings page (the gear icon to the right of the host name in each host details screen).


<h4 id="config-file-roles">roles</h4>
The host's service and role can be configured. If the service or role specified in the configuration file does not exist when the agent launches, it will be newly created. Also, changes made from the web screen will not be synchronized with the agent's configuration file.

**Example: Linking the “app” role of “My-Service” as well as the “db” role of “Another-Service”**

```config
# /etc/mackerel-agent/mackerel-agent.conf
roles = [ "My-Service:app", "Another-Service:db" ]
```


<h4 id="config-file-proxy">proxy</h4>
By specifying this option, you can set an HTTP/HTTPS Proxy for the agent to use for communication. Using this option, it is possible to monitor the server via a proxy server even in a network environment where the monitored server can not directly communicate through the internet.

**Example: When a proxy server that you want to go through is provided with localhost:8080**

```config
# /etc/mackerel-agent/mackerel-agent.conf
http_proxy = "http://localhost:8080"
```

The environment variable `HTTP_PROXY` is also supported. For example, you can gain access via an HTTP Proxy by specifying the following.

```sh
% HTTP_PROXY=http://localhost:8080 mackerel-agent
```

 By appending an environment variable to the file `/etc/sysconfig/mackerel-agent` for yum/rpm packages and `/etc/default/mackerel-agent` for apt/deb packages,  it’s possible to apply this to the mackerel-agent. For example, you can specify the following.

```
HTTP_PROXY="http://localhost:8080/"
```

By writing `https_proxy` in the same format as `http_proxy` in the configuration file, you can define the proxy server settings for HTTP and HTTPS separately.

**Example: When a proxy server that you want to go through is provided with localhost:8080 and localhost:8081**

```config
# /etc/mackerel-agent/mackerel-agent.conf
http_proxy = "http://localhost:8080"
https_proxy = "http://localhost:8081"
```

The correspondence between the `http_proxy` / `https_proxy` settings and the proxy that mackerel-agent uses for communication is as follows.

| `http_proxy` | `https_proxy` | HTTP communication | HTTPS communication |
|:-------------|:--------------|:-----------------------------|:-------------------------------|
| Not Set      | Not Set       | direct communication         | direct communication           |
| Not Set      | Set           | direct communication         | proxy via `https_proxy` value  |
| Set          | Not Set       | proxy via `http_proxy` value | _proxy via `http_proxy` value_ |
| Set          | Set           | proxy via `http_proxy` value | proxy via `https_proxy` value  |
| Set          | `direct`      | proxy via `http_proxy` value | direct communication           |

- If only `http_proxy` is specified in the configuration file, the value will be used as `https_proxy` as well.
- If you do not want to use HTTPS proxy, specify `direct` for `https_proxy`.


<h4 id="config-file-diagnostic">diagnostic</h4>
By specifying this option, the agent’s memory usage is collected and posted as a custom metric (Diagnostic mode. Default is `false`).

This can also be specified as an agent startup option.

```
mackerel-agent -diagnostic
```

The following metrics can be collected with diagnostic mode enabled.

- `custom.agent.memory.alloc`
- `custom.agent.memory.sys`
- `custom.agent.memory.heapAlloc`
- `custom.agent.memory.heapSys`
- `custom.agent.runtime.goroutine_num`

For more information please refer to the following documents.

- [https://pkg.go.dev/runtime#MemStats](https://pkg.go.dev/runtime#MemStats)
- [https://pkg.go.dev/runtime#NumGoroutine](https://pkg.go.dev/runtime#NumGoroutine)


<h4 id="config-file-include">include</h4>
It is possible to describe mackerel-agent configuration items in another configuration file and have them be read by specifying the `include` option.

- fileglobs can be specified as `/etc/mackerel-agent/conf.d/*.conf`
- As for strings, boolean values, and array settings (`verbose` or` roles`), if these values are present in a configuration file that's read later, they will be overwritten.
- When multiple configuration files match, the order in which they are read is undefined.
- The config item `include` in read configuration files is ignored.


<h4 id="config-file-hoststatus">host_status</h4>
A host's status can be set upon startup of the agent. You can assign different statuses for when the agent starts up and for when it shuts down.

A host's status will be set to `working` when the agent boots up, and to `poweroff` when the agent successfully shuts down.

```config
# /etc/mackerel-agent/mackerel-agent.conf
[host_status]
on_start = "working"
on_stop  = "poweroff"
```

Possible statuses are `working` , `standby` , `maintenance` , and `poweroff`. For more details regarding notifications and monitoring of the host status, refer to [Setting up monitoring and alerts] (https://mackerel.io/docs/entry/howto/alerts#host-statuses).


<h4 id="config-file-filesystems">filesystems</h4>
<h5>ignore</h5>
Metric collection can be excluded from specific file systems.

By designating a regular expression like shown below, metrics from the designated file system will no longer be collected.

```config
# /etc/mackerel-agent/mackerel-agent.conf
[filesystems]
ignore = "/dev/ram.*"
```

<h5>use_mountpoint</h5>
You can obtain filesystem metrics for each mount point by specifying the following in the configuration file.

```config
# /etc/mackerel-agent/mackerel-agent.conf
[filesystems]
use_mountpoint = true
```

<h4 id="config-file-disks">disks</h4>
<h5>ignore</h5>
Metric collection can be excluded from specific disk devices.

By designating a regular expression like shown below, metrics from the designated disk device will no longer be collected.

```config
# /etc/mackerel-agent/mackerel-agent.conf
[disks]
ignore = "loop"
```

<h4 id="config-file-interfaces">interfaces</h4>
<h5>ignore</h5>
Metric collection can be excluded from specific interfaces.

Using a regular expression like shown below, metrics will not be collected from the specified interface.

```config
# /etc/mackerel-agent/mackerel-agent.conf
[interfaces]
ignore = "bond.*"
```

For Windows, specify a regular expression that corresponds to the interface name displayed in Windows Device Manager.

<h4 id="config-file-custommetrics">[plugin.metrics.{name}]</h4>
In the section `plugin.metrics. {name}`, you can write configurations to retrieve and post arbitrary metrics.

The agent executes the command specified by the `command` , receives the result as a standard output file, and posts it to the Mackerel service. Because of this structure, the use of other optional user scripts and plugins is possible. For more details, refer to [Posting user-defined custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics).


<h4 id="config-file-customchecks">[plugin.checks.{name}]</h4>
In the section `plugin.checks. {name}`, you can write configurations to execute arbitrary check monitor processing and post the results.
"Check monitor processing" is a type of monitoring that let's you know whether or not a specific process is running or if there's a problematic output in the target log file. The results posted with this configuration will not be graphed.

Because of this structure, the use of other optional user scripts and plugins is possible. For more details, refer to [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks).


<h4 id="config-file-metadata">[plugin.metadata.{name}]</h4>
In the section `plugin.metadata. {name}`, you can write configurations to execute arbitrary processing and register the results in metadata. For more details, refer to [Using metadata](https://mackerel.io/docs/entry/howto/metadata).

<h2 id="option">Startup options</h2>

The following startup options can be specified. If the same items are specified in the configuration file, the startup option items will take precedence.

- `-apikey=API_KEY` This is the API key that a host is identified by on the web. This key is how Mackerel knows which organization a host belongs to. (This is not recommended. We recommend that you include this in the configuration file from version 0.5.1.)
- `-conf=/etc/mackerel-agent/mackerel-agent.conf` This is the settings file path. If you haven’t specified this yet,`/etc/mackerel-agent/mackerel-agent.conf`will be read by default.
- `-pidfile=/var/run/mackerel-agent.pid` This is the PID file path.
- `-root=/var/lib/mackerel-agent` This is the path of the directory where the status of the mackerel-agent is recorded. Currently only the ID file that identifies the host will be placed here.
- `-role=<service>:<role>` This designates the roles and services a role is assigned to.
- `-diagnostic` metrics for the agent itself will be collected and posted.

<h2 id="faq">FAQ</h2>

###How does the agent distinguish between different hosts?

The agent tells one host from another by looking at the host ID which is stored in `/var/lib/mackerel-agent/id`. A host’s name cannot be used to distinguish it. (If the settings file is changing the root with the boot options, an ID file will be created under that root.)

###The agent won’t launch.

If there is an incorrect ID stored in `/var/lib/mackerel-agent/id` the agent will not launch properly. Delete `/var/lib/mackerel-agent/id` and try launching again. (This will recognize it as a new host.) (If the settings file is changing the root with the boot options, an ID file will be created under that root.)

###Can multibyte characters be used?

Yes, they can be used. However, please note that the conf file character code (encoding) must be `UTF - 8`.


## Sub Commands

mackerel-agent has several commands that can be used.

### retire

```
mackerel-agent retire [-conf <config-file>] [-force] [options]
```

This command will retire the host. When executed a confirmation prompt will be displayed. Attaching the `-force` option will bypass the confirmation prompt and retire the host.

### configtest

```
mackerel-agent configtest
```

Check the syntax of the configuration file (`mackerel-agent.conf`).


### once

```
mackerel-agent once
```

This will execute metric collection and display standard output just one time. Metrics will not be posted.

## Settings file for init script

By appending environment variable settings to, for yum/rpm packages `/etc/sysconfig/mackerel-agent`, for apt/deb packages `/etc/default/mackerel-agent`,  it’s possible to have them applied to the mackerel-agent. The following variables can be configured.

### OTHER_OPTS

This will assign the option you want to add in the mackerel-agent. For example, by making an assignment like `OTHER_OPTS=”diagnostic-verbose”`, diagnostics mode will be activated and the DEBUG log will be outputted.

### AUTO_RETIREMENT

By assigning a character string other than 0 to this variable,  the host will be automatically retired upon OS shutdown (or more specifically `/etc/init.d/mackerel-agent stop`).

**Caution** Even if you have manually typed `/etc/init.d/mackerel-agent stop`, if the above assignment has already been made, the host will be retired. If you want to, say, update the agent to the latest version or start/stop the agent, please use  `/etc/init.d/mackerel-agent reload`.

[TOML]: https://github.com/mojombo/toml

## Windows system environment variable 

### MACKEREL_AUTO_RETIREMENT

If a non-zero character string is specified in the system environment variable of Windows, the host is automatically retired upon OS shutdown. Retirement processing is not executed when the service is stopped.
