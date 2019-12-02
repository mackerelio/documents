---
Title: Monitoring Containers
Date: 2019-02-07T17:45:37+09:00
URL: https://mackerel.io/docs/entry/howto/container-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/98012380860618811
---

Use mackerel-container-agent to monitor containers on container orchestration platforms such as Amazon ECS and Kubernetes.

## Features

mackerel-container-agent has the following features.

- A monitoring agent dedicated for container orchestration platforms
- Provided in Docker image
- Monitors tasks (ECS) and Pods (Kubernetes)
- Runs as a task/Pod sidecar 
- Tasks / Pods can be treated as hosts, services and roles can be assigned
- Automatically retires when the agent finishes

The following items are **not supported to monitoring** with mackerel-container-agent.

- Monitoring of the container host
  - container host CPU, memory, network, etc.
- Monitoring of the container orchestration platform itself
  - Nodes, tasks, number of pods, etc.
- Monitoring of containers other than the supported container orchestration platforms

#### Point to note regarding billing

With the use of mackerel-container-agent, for every task or Pod, one host will be registered in Mackerel. Please note that charges will occur if using a paid plan. For more details, refer to [FAQ · Calculating the number of hosts](https://mackerel.io/docs/entry/faq/contracts/calculate-host-number).

## Supported container orchestration platforms

The following container orchestration platforms are supported with mackerel-container-agent.

- Amazon Elastic Container Service(ECS)
  - EC2 Launch Type
  - Fargate Launch Type
  - **Windows containers not supported**
- Kubernetes

## Setup

The setup process for mackerel-container-agent varies for each container orchestration platform.

- [ECS](https://mackerel.io/docs/entry/howto/install-agent/container/ecs)
- [Kubernetes](https://mackerel.io/docs/entry/howto/install-agent/container/kubernetes)

Please note that the following processes are deprecated for mackerel-container-agent v0.1.0 or later.

- [(Deprecated) ECS(default, bridge, host, none network mode)](https://mackerel.io/docs/entry/howto/install-agent/container/ecsbasic)
- [(Deprecated) Fargate, ECS(awsvpc network mode)](https://mackerel.io/docs/entry/howto/install-agent/container/ecsawsvpc)
- [(Deprecated) ECS(Regardless of statup type and network mode)](https://mackerel.io/docs/entry/howto/install-agent/container/ecsv3)

## Obtaining metrics

The metrics that can be obtained with mackerel-container-agent are as follows.
Content may vary depending on the container orchestration platform.

| Metric | Explanation | Metric Name |
| :-- | :-- | :-- |
| CPU Usage | The CPU usage per container (1 core=100%) | container.cpu.&lt;container_name&gt;.usage |
| CPU Limit | The CPU limit per container (1 core=100%)。<br>For Kubernetes, the value of `resources.limits.cpu`. If not defined, the number of cores of the host.<br> For ECS and Fargate, the task CPU. If not defined, the number of CPU cores of the host. | container.cpu.&lt;container_name&gt;.limit |
| Memory Usage | The memory usage per container | container.memory.&lt;container_name&gt;.usage |
| Memory Limit | The memory limit per container.<br>For Kubernetes, the value of `resources.limits.memory`. If not defined, the amount of memory of the host.<br> For ECS and Fargate, the memory limit of the container. If not defined, the task memory. If neither container or memory are defined, the amount of memory of the host.| container.memory.&lt;container_name&gt;.limit |
| Interface Rx | The number of bytes received per task / Pod interface. <br>For ECS(default, bridge network mode), the number of bytes received per container. | interface.&lt;container_name&gt;.rxBytes.delta |
| Interface Tx | The number of bytes sent per task / Pod interface. <br>For ECS(default, bridge network mode), The number of bytes sent per container.| interface.&lt;container_name&gt;.txBytes.delta |

### Role metrics

Role graphs display the total value of container metrics that belong to tasks and pods for both CPU and Memory.

## Obtaining metadata

mackerel-container-agent retrieves information from each container orchestration platform and registers it as host metadata.

- ECS, Fargate
  - Cluster name, ARN, task resource limit, container name, DockerID, etc.
- Kubernetes
  - Pod name, namespace, label, container name, container resource limit, etc.

## Agent configuration

Agent configurations are done with environment variables and the configuration file.
If not using the plugin, the agent can be used with just environment variable configuration.

### Using environment variables 

| Variable name | Explanation |
| :-- | :-- |
| MACKEREL_APIKEY | Specifies the API key used by the agent to communicate with the Mackerel service. |
| MACKEREL_APIBASE | Specifies the Mackerel API endpoint(Default: `https://api.mackerelio.com/`). |
| MACKEREL_ROLES | Sets services and roles for tasks and pods. |
| MACKEREL_AGENT_CONFIG | Sets the agent configuration file. Details for this will be described later. |
| MACKEREL_AGENT_CONFIG_POLLING_DURATION_MINUTES | Specifies time interval of retrieval in minutes for detecting changes in the agent configuration file. |
| MACKEREL_IGNORE_CONTAINER | Sets the name of the container to be excluded from monitoring with regular expressions. |
| MACKEREL_HOST_STATUS_ON_START | When set, the host status changes to the specified value upon startup of the agent. Valid values are "standby", "working", "maintenance", and "poweroff". |
| HTTP_PROXY(HTTPS_PROXY) | Sets an HTTP Proxy that the agent uses to communicate outside of the container. Note that this is a different setting from the http probe proxy (described later). |


By default, the configuration file is read once at startup. With `MACKEREL_AGENT_CONFIG_POLLING_DURATION_MINUTES` configured, the configuration file is periodically retrieved to detect and apply changes.

### Using the configuration file

Set the file path of the agent configuration file with the environment variable `MACKEREL_AGENT_CONFIG`.
If `MACKEREL_AGENT_CONFIG` is not specified, the default setting is used.

Possible paths for `MACKEREL_AGENT_CONFIG` are as follows.

- File path: `/etc/mackerel/mackerel.yaml`
- HTTP/HTTPS: `https://example.com/mackerel/conf/mackerel.yaml`
- Amazon S3: `s3://bucket/mackerel/conf/mackerel.yaml`

Write the configuration file in YAML format. The following is an example.

```
apikey: "YOUR_MACKEREL_APIKEY"
roles:
  - "My-Service:app"
  - "Another-Service:db"
ignoreContainer: '\Amackerel-container-agent\z'
```

#### Configuration items

| Item name | Explanation |
| :-- | :-- |
| apikey | Specifies the API key used by the agent to communicate with the Mackerel service. |
| apibase | The Mackerel API end point (Default: `https://api.mackerelio.com/`). |
| roles | Sets services and roles for tasks and pods. |
| ignoreContainer | Sets the name of the container to be excluded from monitoring with regular expressions. |
| root | Specifies the mackerel-container-agent root directory (Default: `/var/tmp/mackerel-container-agent`)。 |
| plugin.metrics | Sets a plugin to get and post arbitrary metrics. Details for this will be described later. |
| plugin.checks | Sets the plugin to execute arbitrary check monitors and post the results. Details for this will be described later. |
| readinessProbe | Sets the Readiness Probe. Details for this will be described later. |

### Configuration priority 

If the same item is defined in the environment variable as the configuration file, ***the configuration file value is given priority***. Please take this into account.

### Using plugins

Metric plugins and check plugins are able to be used.

mackerel-container-agent also offers a [Docker image that bundles official plugins](https://hub.docker.com/r/mackerel/mackerel-container-agent/tags).

`plugins` is an image that bundles the plugins in the `latest` image, and `vX.Y.Z-plugins` is an image that bundles plugins in the `vX.Y.Z` image.

The plugins bundled in this image are as follows.

- [mackerel-agent-plugins](https://github.com/mackerelio/mackerel-agent-plugins)
  - mackerel-plugin-apache2
  - mackerel-plugin-elasticsearch
  - mackerel-plugin-fluentd
  - mackerel-plugin-gostats
  - mackerel-plugin-haproxy
  - mackerel-plugin-jmx-jolokia
  - mackerel-plugin-memcached
  - mackerel-plugin-mysql
  - mackerel-plugin-nginx
  - mackerel-plugin-php-apc
  - mackerel-plugin-php-fpm
  - mackerel-plugin-php-opcache
  - mackerel-plugin-plack
  - mackerel-plugin-postgres
  - mackerel-plugin-redis
  - mackerel-plugin-sidekiq
  - mackerel-plugin-snmp
  - mackerel-plugin-squid
  - mackerel-plugin-uwsgi-vassal
- [mackerel-plugin-json](https://github.com/mackerelio/mackerel-plugin-json)
- [go-check-plugins](https://github.com/mackerelio/go-check-plugins)
  - check-cert-file
  - check-elasticsearch
  - check-file-age
  - check-file-size
  - check-http
  - check-jmx-jolokia
  - check-log
  - check-memcached
  - check-mysql
  - check-postgresql
  - check-redis
  - check-ssh
  - check-ssl-cert
  - check-tcp

If you would like to use a plugin other than the ones bundled, prepare an image with mackerel/mackerel-container-agent as the base and installed with the desired plugin.

#### Usable plugin configurations

Usable configurations for mackerel-container-agent are as follows.

Metric plugin configuration items (`plugin.metrics`)

- **command**: A command that the agent regularly executes (required).
- **user**: The user to execute commands. If not specified,  the user that executed mackerel-container-agent.
- **env**: The environment variable to pass to the command.
- **timeoutSeconds**: Specifies when a command times out in seconds. The default value is 30 seconds.

Check plugin configuration items (`plugin.checks`)

- **command**: A command that the agent regularly executes (required).
- **user**: The user to execute commands. If not specified,  the user that executed mackerel-container-agent.
- **env**: The environment variable to pass to the command.
- **timeoutSeconds**: Specifies when a command times out in seconds. The default value is 30 seconds.
- **memo**: Notes can be set for check monitors. The character string specified here can be checked in the alert notification, the alert details screen, or the host details page.

#### Plugin configuration examples

The following are examples of plugin configurations.

```
plugin:
  metrics:
    mysql:
      command: mackerel-plugin-mysql

    redis6379:
      command: mackerel-plugin-redis -port=6379 -timeout=5 -metric-key-prefix=redis6379
      timeoutSeconds: 50

    sample:
      command: ruby /usr/local/bin/sample-plugin.rb
      user: "sample-user"
      env:
        FOO: "FOO BAR"
        QUX: 'QUX QUUX'

  checks:
    procs:
      command: "check-procs --pattern=/usr/sbin/sshd --warning-under=1"
      user: "sample-user"
      env:
        FOO: FOO BAR
      timeoutSeconds: 45
      memo: "check procs memo"
```

### Readiness Probe configuration
When the agent starts up, you can check whether the application is running normally. After a successful confirmation, a host is created.

There are three types of Readiness Probes that can be configured with mackerel-container-agent. Only one can be configured at a time.

- exec probe
  - Executes a command and checks whether it ends normally
- http probe
  - Sends an HTTP request and checks whether the response is normal
- tcp probe
  - Check whether a connection with TCP is possible

Shared configuration items (`readinessProbe`)

- **timeoutSeconds**: Specifies when a command or HTTP request times out in seconds. The default value is 1 second.
- **initialDelaySeconds**: Specifies the time between starting the agent and when the probe check takes place in seconds. The default value is 0 seconds.
- **periodSeconds**: Specifies the interval for the probe check in seconds. The default value is 10 seconds.

#### exec probe
The exec probe executes the command and judges it as normal when the exit status is 0.

exec probe configuration items (`readinessProbe.exec`)

- **command**: Specifies the command to execute (required)。
- **user**: The user to execute the command. If not specified, the user that executed mackerel-container-agent.
- **env**: The environment variable to pass to the command.

Configuration example
```
readinessProbe:
  exec:
    command: cat /tmp/healthy
```
```
readinessProbe:
  exec:
    command: cat /tmp/healthy
    user: "sample-user"
    env:
      FOO: "FOO BAR"
  initialDelaySeconds: 10
  timeoutSeconds: 5
  periodSeconds: 3
```

#### http probe
The http probe sends an HTTP request and judges it as normal when the status is either of the 200 or 300 series.

http probe configuration items (`readinessProbe.http`)

- **path**: Specifies the path of the request contact (required).
- **scheme**: Specifies the URI scheme. The default value is `http`.
- **method**: Specifies the method of the request. The default value is `GET`.
- **host**: Specifies the host of the request contact. The default value is `localhost`.
- **port**: Specifies the port of the request contact. The default value is `80`.
- **headers**: Specifies the request header.
- **proxy**: Specifies the HTTP Proxy URL.

Configuration example
```
readinessProbe:
  http:
    path: /healthy
    port: 8000
```
```
readinessProbe:
  http:
    scheme: http
    method: PUT
    path: /healthy
    headers:
      - name: X-Custom-Header
        value: test
    proxy: "https://proxy.example.com:8080"
  initialDelaySeconds: 10
  timeoutSeconds: 5
  periodSeconds: 10
```

#### tcp probe
The tcp probe judges as normal when a TCP connection can be made.

tcp probe configuration items (`readinessProbe.tcp`)

- **port**: Specifies the port of the request contact (required).
- **host**: Specifies the host of the request contact.

Configuration example
```
readinessProbe:
  tcp:
    port: 53
```
```
readinessProbe:
  tcp:
    port: 443
    host: example.com
  initialDelaySeconds: 10
  timeoutSeconds: 5
  periodSeconds: 10
```

## Monitoring

The following metric monitoring is used to monitor containers. If alerts occur for containers for which monitoring is unnecessary, please exclude them with the environment variable `MACKEREL_IGNORE_CONTAINER` or `ignoreContainer` in the configuration file.

- CPU: `Container CPU %`
  - Monitors the **maximum** CPU usage rate (`container.cpu.<container_name>.usage / container.cpu.<container_name>.limit`) of each container.
- Memory: `Container Memory %`
  - Monitors the **maximum** Memory usage rate (`container.memory.<container_name>.usage / container.memory.<container_name>.limit`) of each container.
- interface: `interface.<container_name>.rxBytes.delta`, `interface.<container_name>.txBytes.delta`
  - Monitors the network interface transmission quantity.
