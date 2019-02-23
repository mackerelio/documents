---
Title: Using the mkr CLI tool
Date: 2015-06-19T16:03:20+09:00
URL: https://mackerel.io/docs/entry/advanced/cli
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450098145424
---

With the mkr CLI tool, it is possible to perform operations such as changing all host statuses at one time as well as creating built-in protocols within scripts for automation. 

mkr is now available on GitHub.
https://github.com/mackerelio/mkr

## Getting everything set up

### Install
When you use yum or apt to install mkr, you firstly have to register the Mackerel package repository, which is used for mackerel-agent.

- Amazon Linux: [Installing mackerel-agent in Amazon Linux](https://mackerel.io/docs/entry/howto/install-agent/amazon-linux).
- Redhat OS systems・RPM package (yum): [Installing mackerel-agent in CentOS / RedHat](https://mackerel.io/docs/entry/howto/install-agent/rpm).
- Debian OS systems・deb package (apt): [Installing mackerel-agent in Ubuntu / Debian](https://mackerel.io/docs/entry/howto/install-agent/deb).

#### use yum

```cdl
% yum install mkr
```

#### use apt

```cdl
% apt-get install mkr
```

#### use brew

```cdl
% brew tap mackerelio/mackerel-agent
% brew install mkr
```
#### build with go

```cdl
% go get github.com/mackerelio/mkr
```

### Setup

To use mkr, first we will assign the API key to the environment variable.
```cdl
export MACKEREL_APIKEY=<API key>
```


## How to use mkr

For a more in-depth tutorial, refer to https://github.com/mackerelio/mkr 

### Hosts

For example, specify a hostId to get that host's information. 
```cdl
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

```cdl
mkr update --status maintenance <hostIds>...
```

...or get a list of hosts.
```cdl
mkr hosts --service My-Service --role proxy
```

By putting these together, it's possible to change the status of all hosts in the designated services and roles.

```cdl
mkr update --st working $(mkr hosts -s My-Service -r proxy | jq -r '.[].id')
```

### Fetch metrics

With mkr, each of the metric can be fetched by the `metrics` command.
For example, specify a hostId to get that host's metric, or specify a service name to get that service's metric. 

```cdl
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
```cdl
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

### Generating dashboards

In mkr, custom dashboards can be automatically generated from the definition file with the dashboards subcommand. 

The definition file is in YAML format.

#### Execution example

By specifying the definition file with parameters as shown below and executing it, a custom dashboard will automatically be generated inside the organization of the corresponding  API key configured in mkr. 
```cdl
% mkr dashboards generate config.yml
```

Additionally, by specifying the `--print` option, outputting the dashboard’s markdown to standard output can be done without registering or updating in Mackerel. 
```cdl
% mkr dashboards generate config.yml --print
```
#### Definition file (YAML) specifications 

There are two types of dashboards that can be generated by mkr.

1. A list of host graphs specified by hosts and metrics
2. A list of graphs that are individually specified by graph type such as service metric and expression graphs.

An example for the format of each follows below.

##### Host graphs
```yaml
config_version: 0.9
url_path: custom-graph
title: Custom Host Graphs
format: iframe
height: 200
width: 400
host_graphs:
  - headline: host graphs 1
    host_ids:
     - XXXXXXXXXXX
     - YYYYYYYYYYY
     - ZZZZZZZZZZZ
    graph_names:
     - loadavg5
     - cpu
     - filesystem
     - custom.linux.disk.elapsed.*
    period: 30m
  - headline: host graphs 2
    host_ids:
     - XXXXXXXXXXX
    graph_names:
     - loadavg5
     - memory
    period: 30m
```

##### Individually specified graphs
```yaml
config_version: 0.9
url_path: custom-graph
title: Custom Graphs
format: iframe
graphs:
  - headline: any graphs
    column_count: 2
    graph_def:
     - service_name: sugoi-service
       graph_name: custom.access_latency.*
     - service_name: sugoi-service
       role_name: api
       graph_name: loadavg5
       stacked: false
       simplified: true
       period: 6h
     - host_id: XXXXXXXXX
       graph_name: custom.inode.count.#.*
       period: 30m
     - host_id: XXXXXXXXX
       graph_name: filesystem
       period: 30m
  - headline: expression graph
    graph_def:
     - query: avg(roleSlots('sugoi-service:api',sum(group(host($HOST_ID, 'cpu.system.percentage'), host($HOST_ID, 'cpu.user.percentage')))))
       period: 30m
       title: graph title
       unit: float
```

#### YAML items

##### Common
| item name          | type | description  |
|--------------|--|--------------------------------------|
| config_version | string |The version of yaml’s definition file.  Currently fixed at 0.9 |
| url_path       | string | URL of the custom dashboard. Using the above  example, the url will be:`https://mackerel.io/orgs/<orgname>/dashboards/custom-graph` |
| title          | string | title of the custom dashboard |
| format         | string | [optional] graph format. `iframe` or `image`. The default is `iframe`. |
| height         | int | [optional] graph height. Only available in `iframe format`. The default value is 200. |
| width          | int | [optional] graph width. Only available in `iframe` format. The default value is 400. |
| host_graphs    | array[host_graphs] | Definitions of the host graphs continue below in array format. Either this or `graphs` can be specified. |
| graphs         | array[graphs] | Definitions of the individual graph specifications continue below in array format. Either this or `host_graphs` can be specified. |

##### Host graph definitions
Here are the above-mentioned definitions for `host_graphs` in array format.

| item name          | type | description  |
|--------------|--|--------------------------------------|
| headline       | string | Table headline |
| host_ids       | array[string] | List of hosts to display in the list. Specified in array format. |
| graph_names    | array[string] | List of graphs to display in the list. Specified in array format. |
| period         | string | [optional] graph display period. The default value is "1h". Available units are `m`, `h`, `d`, `w`, `mo`, `y`. |


##### Individual graph definitions
Here are the above-mentioned definitions for `graphs` in array format. Depending on what is specified, the following graph formats are supported. 

- service graphs when only `service_name` is specified 
- role graphs when `service_name` or `role_name` is specified 
- host graph when `host_id` is specified
- expression graphs when `query` is specified
- error if none of the above is met (i.e. not a service, role, host, or expression graph)

| item name          | type | description  |
|--------------  |------|--------------------------------------|
| headline       | string | Table headline |
| column_count   | int | [optional] The number of columns in the graph generated. The default value is 1.  |
| graph_def      | array[graph_def] | each graph’s definitions are specified below in array format |
| service_name   | string | service name |
| role_name      | string | role name   |
| host_id        | string | host ID |
| query          | string | expression |
| title          | string | [optional] Can only be specified when an expression graph (query) is specified. Specify the title of the expression graph |
| unit           | string | [optional] Can only be specified when an expression graph (query) is specified. Specify the metric unit of the expression graph |
| graph_name     | string | graph name |
| period         | string | [optional] graph display period. The default value is "1h". Available units are`m`, `h`, `d`, `w`, `mo`, `y`. |
| stacked        | boolean | [optional] Whether or not it’s a stacked graph. The default is false. Available for role graphs only. |
| simplified     | boolean | [optional] Whether or not the display is simplified. The default is false. Available for role graphs only. |

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
