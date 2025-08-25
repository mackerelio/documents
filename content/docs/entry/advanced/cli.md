---
Title: Using the mkr CLI tool
Date: 2015-06-19T16:03:20+09:00
URL: https://mackerel.io/docs/entry/advanced/cli
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450098145424
---

With the mkr CLI tool, it's possible to perform operations such as changing all the host statuses at once or creating built-in protocols within scripts for automation. 

mkr is now available on GitHub.
https://github.com/mackerelio/mkr

[:contents]

## Introducing mkr

### For Linux-based OS

To use mkr, you need to install it. If you install using yum or apt, you need to add the Mackerel package repository beforehand. The Mackerel package repository is automatically added when you run the mackerel-agent install command. Please refer to the following help file for details on how to install mackerel-agent.

- [Installing mackerel-agent in Amazon Linux](https://mackerel.io/docs/entry/howto/install-agent/amazon-linux)
- [nstalling mackerel-agent in Red Hat Enterprise Linux derivatives](https://mackerel.io/docs/entry/howto/install-agent/rpm)
- [Installing mackerel-agent in Ubuntu / Debian](https://mackerel.io/docs/entry/howto/install-agent/deb)

#### Using yum

```
% yum install mkr
```

#### Using apt

```
% apt-get install mkr
```

#### Using brew

```
% brew install mackerelio/mackerel-agent/mkr
```
#### Build with go

```
% go install github.com/mackerelio/mkr@latest
```

### For Windows

You can use mkr.exe as it is included in the mackerel-agent installer (msi file). mkr.exe is installed in the same folder as mackerel-agent.exe. However, since %PATH% is not configured, either specify with a full path or add this folder to the %PATH% at the time of use.

[https://mackerel.io/docs/entry/howto/install-agent/msi:embed:cite]

### API Key Settings

API key setting is required to use mkr. If mackerel-agent is installed, the API key described in mackerel-agent.conf is used by default. If you want to use it in an environment where mackerel-agent is not installed, you need to set the API key as an environment variable as shown below.

For Linux-based OS

```
export MACKEREL_APIKEY=<API key>
```

For Windows

```
set MACKEREL_APIKEY=<API key>
```

## How to use mkr

For a more in-depth tutorial, refer to https://github.com/mackerelio/mkr 

### Hosts

For example, specify a hostId to get that host's information. 
```
mkr status <hostId>
```

```json
$ mkr status 2eQGEaLxibb
{
    "id": "2eQGEaLxibb",
    "name": "myproxy001",
    "status": "standby",
    "roleFullnames": [
        "My-Service:proxy"
    ],
    "isRetired": false,
    "createdAt": "2014-11-15T21:41:00+09:00"
}
```

Also, it's possible to change one or more host's statuses by specifying hostId's...

```
mkr update --status maintenance <hostIds>...
```

...or get a list of hosts.
```
mkr hosts --service My-Service --role proxy
```

By putting these together, it's possible to change the status of all hosts in the designated services and roles.

```
mkr update --st working $(mkr hosts -s My-Service -r proxy | jq -r '.[].id')
```

In addition, you can create standard host as need. For example, you can create network devices that cannot use mackerel-agent or cloud integration as hosts, and then use the API to post metrics and check monitoring results for those hosts.

```
mkr create --status working --roleFullname 'My-Service:Router' --memo 'Gateway router. Metrics are posted from outside using mkr.' MyServiceRouter01
```

### Get metric names

The name of the metric a host or service has can be obtained by the `metric-names` subcommand.

```
mkr metric-names -H <hostId>
mkr metric-names -s <serviceName>
```

#### Execution example
- Get host metric names
```
% mkr metric-names -H 2eQGEaLxibb
[
    "cpu.guest.percentage",
    "cpu.idle.percentage",
    "cpu.iowait.percentage",
    ...
]
```

- Get service metric names
```
% mkr metric-names -s myservice
[
    "Sample.foo"
    "Sample.bar"
    ...
]
```

A list of host metric names can also be gotten by appending `-v` to the `status` subcommand.

```
% mkr status -v 2eQGEaLxibb
{
    "id": "2eQGEaLxibb",
    ...
    "metrics": [
        "cpu.guest.percentage",
        ...
        "memory.used"
    ]
}
```

### Fetch metrics

With mkr, each of the metric can be fetched by the `metrics` command.
For example, specify a hostId to get that host's metric, or specify a service name to get that service's metric. 

```
mkr metrics --host <hostId> --name <name> --from <epoch seconds> --to <epoch seconds>  
```

```json
% mkr metrics --host 39gQhpp3Wpj --name cpu.user.percentage --from 1508909640 --to 1508909760
[
    {
        "time": 1508909640,
        "value": 14
    },
    {
        "time": 1508909700,
        "value": 12
    },
    {
        "time": 1508909760,
        "value": 27
    }
]
```

By specifying epoch seconds with `--from`, `--to` options, you can specify the range of metrics to fetch.
If not specified `--from` or `--to` options, `--from` is set to be 0, and `--to` is set to be current time.

```json
% mkr metrics --service myservice --name Sample.foo
[
    {
        "time": 1508912100,
        "value": 30
    }
]
```

### Post metrics

With mkr, you can post a metric with the throw subcommand.  
You can post a host metric by specifying a host ID or a service metric by specifying a service name.  
The throw subcommand accepts metrics to be posted via standard input. For information on the format of the metric, see  [Posting metrics - Host Posting Custom Metrics - Mackerel Help](https://mackerel.io/docs/entry/advanced/custom-metrics#post-metric).

The following is an example of posting the value 1 to the host metric `custom.example.throwA` for the host ID `2eQGEaLxibb`.

```
% echo -e "example.throwA\t1\t$(date +%s)" | mkr throw --host 2eQGEaLxibb
```

If successful, the following message will be displayed.

```
    thrown 54EwSZbNexW 'custom.example.throwA       1.000000        1711596750'
```

The following is an example of posting the value 2 to the service metric `example.throwB` for the service `myservice`.

```
% echo -e "example.throwB\t2\t$(date +%s)" | mkr throw --service myservice
```

If successful, the following message will be displayed.

```
    thrown myservice 'example.throwB   2.000000        1711596974'
```

You may submit multiple metrics at the same time.
As an example, to post a value of 1 for `custom.example.throwC` and a value of 2 for `custom.example.throwD`, use `\n` to prepare output with line breaks as follows.

```
% echo -e "example.throwC\t1\t$(date +%s)\nexample.throwD\t2\t$(date +%s)"
example.throwC  1       1713273764
example.throwD  2       1713273764
```

By passing the result of this output to the standard input of the `mkr throw` command, two metrics, `custom.example.throwC` and `custom.example.throwD`, are posted simultaneously.

```
% echo -e "example.throwC\t1\t$(date +%s)\nexample.throwD\t2\t$(date +%s)" | mkr throw --host 2eQGEaLxibb
```

Metric plugins that can post metrics from mackerel-agent, such as the official metric plugins listed in  [Metric plugins list - Mackerel Docs](https://mackerel.io/docs/entry/plugins/metric-plugins-list), output metrics that conform to the format accepted by `mkr throw`,so you can submit it by passing the execution result to the standard input as shown below.

```
% mackerel-plugin-mysql | mkr throw --host 2eQGEaLxibb
```

### Monitoring rule

With mkr, monitoring rules can be operated with the `monitors` subcommand. This `mkr monitors` subcommand also comes with three subcommands, `pull`, `diff`, and `push`. If none of these subcommands are specified and the user simply types `mkr monitors`, the list of monitoring rules will be displayed.

- `pull`
  - gets the monitoring rule list from Mackerel and saves it in a local file.
- `diff`
  - displays the difference between the monitoring rule list saved in Mackerel and a local file.
- `push`
  - syncs a local file’s monitoring rule list to Mackerel’s.

For more information about JSON format please refer to [Registering monitor configuration in API specs](https://mackerel.io/api-docs/entry/monitors#create).

#### Identification logic of monitoring rules upon push

When doing a `pull`, the `id` that was given by Mackerel will always be included.
When doing a `push`, the following monitoring rule identification logic will carried out so that rules that don’t include an `id` will also be permitted.

- `id` included... identication by `id`
- `id` not included but `name` is included… identification by `name`
  - if more than one monitoring rule with the same `name` exist, identification by `id` only will be carried out. For that it is necessary to include an `id` in every monitoring rule. A warning message will also be outputted.
- if a monitoring rule includes neither a `name` nor an `id`, an error will be produced.

#### Execution example

- `diff` when there is no difference between the local file and Mackerel
```
% ./mkr monitors diff
Summary: 0 modify, 0 append, 0 remove
```

- `diff` when there is a difference between the local file and Mackerel
```diff
% ./mkr monitors diff
Summary: 1 modify, 1 append, 1 remove

  {
   "name": "Filesystem %",
   "type": "host",
   "metric": "disk%",
   "operator": ">",
-  "warning": 95.000000,
+  "warning": 96.000000,
   "critical": 99.000000,
   "duration": 3,
   "url": "",
    "scopes": [
    ],
    "excludeScopes": [
    ],
  },
- {
-   "id": "<id>",
-   "name": "loadavg5",
-   "type": "host",
-   "metric": "loadavg5",
-   "operator": ">",
-   "warning": 1.000000,
-   "critical": 5.000000,
-   "duration": 3,
-   "url": "",
-   "scopes": [
-     "My-Service:proxy"
-   ],
-   "excludeScopes": [
-   ],
- },
+ {
+   "name": "loadavg",
+   "type": "host",
+   "metric": "loadavg5",
+   "operator": ">",
+   "warning": 1.000000,
+   "critical": 5.000000,
+   "duration": 5,
+   "url": "",
+   "scopes": [
+     "My-Service:proxy"
+   ],
+   "excludeScopes": [
+   ],
+ },
```

- example of `push`
```json
% mkr monitors push --dry-run -F monitors_new.json
      info Create a new rule.
 {
   "name": "loadavg",
   "type": "host",
   "metric": "loadavg5",
   "operator": ">",
   "warning": 1.000000,
   "critical": 5.000000,
   "duration": 5,
   "url": "",
   "scopes": [
     "My-Service:proxy"
   ],
   "excludeScopes": [
   ],
 },
      info Delete a rule.
 {
   "id": "<id-1>",
   "name": "loadavg5",
   "type": "host",
   "metric": "loadavg5",
   "operator": ">",
   "warning": 1.000000,
   "critical": 5.000000,
   "duration": 3,
   "url": "",
   "scopes": [
     "My-Service:proxy"
   ],
   "excludeScopes": [
   ],
 },
      info Update a rule.
 {
   "id": "<id-2>",
   "name": "Filesystem %",
   "type": "host",
   "metric": "disk%",
   "operator": ">",
   "warning": 96.000000,
   "critical": 99.000000,
   "duration": 3,
   "url": "",
   "scopes": [
   ],
   "excludeScopes": [
   ],
 },
```

### Alerts

The `mkr alerts` subcommand allows you to perform operations related to alerts.

`mkr alerts` and `mkr alerts list` display alerts that are currently occurring by default, but you can use the `--with-closed` (`-w`) option to list not only alerts that are currently occurring but also closed alerts. You can also filter by service or host status.
  
The difference between `mkr alerts` and `mkr alerts list` is the display format. `mkr alerts` outputs in JSON format, including host IDs and monitoring rule IDs, similar to the API, while `mkr alerts list` displays in a human-readable format with host names and monitoring rule names expanded.

```
% mkr alerts -w
[
    {
        "id": "5uN9FBr6JVw",
        "status": "WARNING",
        "monitorId": "4TG6xGK64PW",
        "type": "host",
        "hostId": "4RXBJ9SQw67",
        "value": 73.68399467417471,
        "openedAt": 1750675925
    },
    {
        "id": "5pjkT5GqhPs",
        "status": "CRITICAL",
        "monitorId": "4TG93ajat7Q",
        "type": "check",
        "hostId": "4RNuLWQKJh1",
        "message": "Procs CRITICAL: \nFound 0 matching processes; cmd /w3wp/",
        "openedAt": 1740972447
    }
]

% mkr alerts list -w
5uN9FBr6JVw 2025-06-23 19:52:05 WARNING  Windows memory-percentage monitor memory% 73.68 > 70.00 EC2AMAZ-1S800NC standby [Win-Demo:DB]
5pjkT5GqhPs 2025-03-03 12:27:27 CRITICAL  Procs CRITICAL: ... EC2AMAZ-GELUS5G working [Win-Demo:Web]
```

By default, `mkr alerts` and `mkr alerts list` retrieve up to 100 alerts from the latest ones, but
you can retrieve more alerts by using the `-l` (`--limit`) option, such as `mkr alerts -l 300`.

The `mkr alerts close` command allows you to close alerts that are currently occurring. Using the `--reason` (`-r`) option allows you to add a memo, similar to closing from the console.

```
% mkr alerts close 5wfKdWBXT8W --reason "Closing manually due to a missed close during the previous response"
Alert closed 5wfKdWBXT8W
```

The `mkr alerts logs` command allows you to retrieve detailed information about an alert by its ID.
The results are returned in JSON format. Using the `--jq` option allows you to filter and format the output using jq syntax.

```
% mkr alerts logs 5wDea1jMahU
[
    {
        "id": "5wDeJ8m839Q",
        "createdAt": 1753945570,
        "status": "OK",
        "trigger": "monitoring",
        "monitorId": "45VugCVZyZw",
        "targetValue": 31
    },
    {
        "id": "5wDea1kw9W5",
        "createdAt": 1753945269,
        "status": "WARNING",
        "trigger": "monitoring",
        "monitorId": "45VugCVZyZw",
        "targetValue": 52.4
    }
]
% mkr alerts logs 5wDea1jMahU --jq '.[].id'
5wDeJ8m839Q
5wDea1kw9W5
```

### Custom Dashboards

mkr lets you manage custom dashboards with the dashboards subcommand. There are two types of subcommands: pull / push. If a subcommand is not specified, the custom dashboard list is displayed.

- `pull`
  - Retrieve a list of custom dashboards from Mackerel and save multiple configuration files in the current directory.
- `push`
  - Apply a local file's custom dashboard configuration file in Mackerel.

Refer to the [Create Dashboards](https://mackerel.io/api-docs/entry/dashboards#create) section of the API documentation for custom dashboard configuration file format.

#### Identification logic for custom dashboards when pushing

A Mackerel assigned `id` is always included when `pulling`.
When `pushing`, the following logic is used so that rules without an `id` are also permitted.

- If an `id` is included, the custom dashboard with the matching id will be updated.
  - An error will occur if there is no dashboard with a matching `id`.
- If an `id` is not included, a new custom dashboard will be created.


#### Execution example

Retrieve custom dashboard list

When retrieving lists, `widgets` are always `null`.

```
% mkr dashboards
[
    {
        "id": "2LHfKUq36xW",
        "title": "demo",
        "urlPath": "2LHfFFsNvo1",
        "createdAt": 1470725548,
        "updatedAt": 1538371379,
        "memo": "",
        "widgets": null,
	},
    {
        "id": "49CBJiNaXfQ",
        "title": "demo2",
        "urlPath": "custom",
        "createdAt": 1470725548,
        "updatedAt": 1538371379,
        "memo": "",
        "widgets": null
	}
]
```

- Save custom dashboards to a local file

```
% mkr dashboards pull
      info Dashboard file is saved to 'dashboard-2LHfKUq36xW.json'(title:demo)
      info Dashboard file is saved to 'dashboard-49CBJiNaXfQ.json'(title:demo2)
% ls
dashboard-2LHfKUq36xW.json	dashboard-49CBJiNaXfQ.json
```

- Update Mackerel custom dashboards

```
% mkr dashboards push --file-path dashboard-49CBJiNaXfQ.json
```

### Graph annotations
Graph annotation related operations can be performed in mkr with the annotations subcommand. There are four types of subcommands: create/list/update/delete. 

For more information on the json format returned by each subcommand, please refer to the [Graph annotations section of the API specifications](https://mackerel.io/api-docs/entry/graph-annotations).

#### create
- The subcommand to create graph annotations
- If creation of the graph annotation is successful, the json representing the created graph annotation will be output
- `service`, `from`, `to`, `title` are required parameters
- `description` and `role`(multiple specifications possible) can also be specified

```sh
% mkr annotations create --service My-Machine --from 1480125301 --to 1486125301 --title "Deploy" --description "Deployed"

{
    "id": "2VpN33ceumh",
    "title": "Deploy",
    "describe": "Deployed",
    "from": 1480125301,
    "to": 1486125301,
    "service": "My-Machine"
}
```

#### list
- The subcommand that returns a list of graph annotations of the specified service name (service) within the specified time period (to and from)
- The json representing the list of graph annotations will be output
- `service`, `from`, `to`, `title` are required parameters

```sh
% mkr annotations list --service My-Machine --from 1480125301 --to 1486125301

[
    {
        "id": "2VpN33ceumh",
        "title": "Deploy",
        "from": 1480125301,
        "to": 1486125301,
        "service": "My-Machine"
    }
]
```

#### update
- The subcommand to update specified graph annotations
- If updating the graph annotation is successful, the json representing the updated graph annotation will be output
- `id`, `service`, `from`, `to`, `title` are required parameters
- `description` and `role`(multiple specifications possible) can also be specified

```sh
% mkr annotations update --id 2VpN33ceumh --service My-Machine --from 1485013461 --to 1485169804 --title "updated" --role Desktop --role Laptop

{
    "id": "2VpN33ceumh",
    "title": "updated",
    "from": 1485013461,
    "to": 1485169804,
    "service": "My-Machine",
    "roles": [
        "Desktop",
        "Laptop"
    ]
}
```

#### delete
- The subcommand to delete graph annotations
- If deletion of the graph annotation is successful, the json representing the deleted graph annotation will be output
- `id` is a required parameter

```sh
% mkr annotations delete --id 2VpN33ceumh

{
    "id": "2VpN33ceumh",
    "title": "updated",
    "from": 1485013461,
    "to": 1485169804,
    "service": "My-Machine",
    "roles": [
        "Desktop",
        "Laptop"
    ]
}
```

For more information please refer to https://github.com/mackerelio/mkr

### Monitor command execution results

With mkr, you can monitor the execution results of commands with the wrap subcommand. For details on how to use it, refer to the following help.

[https://mackerel.io/docs/entry/howto/mkr/wrap:embed:cite]
