---
Title: Using the mkr CLI tool
Date: 2015-06-19T16:03:20+09:00
URL: https://mackerel.io/docs/entry/advanced/cli
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450098145424
---

With the mkr CLI tool, it's possible to perform operations such as changing all the host statuses at once or creating built-in protocols within scripts for automation. 

mkr is now available on GitHub.
https://github.com/mackerelio/mkr

## Getting everything set up

### Install
To install mkr using yum or apt, you first have to register the Mackerel package repository, which is used for mackerel-agent.

- Amazon Linux: [Installing mackerel-agent in Amazon Linux](https://mackerel.io/docs/entry/howto/install-agent/amazon-linux).
- Red Hat Enterprise Linux derivatives・RPM package (yum): [Installing mackerel-agent in Red Hat Enterprise Linux derivatives](https://mackerel.io/docs/entry/howto/install-agent/rpm).
- Debian derivatives・deb package (apt): [Installing mackerel-agent in Ubuntu / Debian](https://mackerel.io/docs/entry/howto/install-agent/deb).

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

#### For Windows

You can use mkr.exe as it is included in the mackerel-agent installer (msi file). mkr.exe is installed in the same folder as mackerel-agent.exe. However, since %PATH% is not configured, either specify with a full path or add this folder to the %PATH% at the time of use.

### Setup

mkr automatically reads the API key from mackerel-agent.conf in the mackerel-agent configuration file. If there is no configuration file or if you want to configure a different API key, specify it with an environment variable. 

```
export MACKEREL_APIKEY=<API key>
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

- If　an `id` is included, the custom dashboard with the matching id will be updated.
  - An error will occur if there is no dashboard with a matching `id`.
- If　an `id` is not included, a new custom dashboard will be created.


#### Execution example

Retrieve custom dashboard list

When retrieving lists, `widgets` are always `null`.

```cdl
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

```cdl
% mkr dashboards pull
      info Dashboard file is saved to 'dashboard-2LHfKUq36xW.json'(title:demo)
      info Dashboard file is saved to 'dashboard-49CBJiNaXfQ.json'(title:demo2)
% ls
dashboard-2LHfKUq36xW.json	dashboard-49CBJiNaXfQ.json
```

- Update Mackerel custom dashboards

```cdl
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
