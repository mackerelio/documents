---
Title: Monitoring Docker
Date: 2015-09-24T15:59:53+09:00
URL: https://mackerel.io/docs/entry/advanced/docker
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415122468366
---

In this document we will be talking about how to monitor Docker ( https://www.docker.com/ ) with Mackerel.

In order to understand Docker container's resource consumption, we will refer to the statistical data of the resource that Docker is using. The statistical data of Docker’s resource is obtained using the API ( https://docs.docker.com/reference/api/engine/ ). Metrics can also be gathered through the cgroup’s file system for backward compatibility, but this way is deprecated. 
For technical details please refer to the official documentation ( https://docs.docker.com/engine/containers/runmetrics/ ).

## Using mackerel-plugin-docker

Using mackerel-plugin-docker you can acquire cgroup statistics and have them graphed in Mackerel as custom metrics graphs.
mackerel-plugin-docker is included in the official plugin pack so let’s go ahead and install that first. For a more specific description of this procedure please refer to [Using the official plugin pack to visualize middleware metrics](https://mackerel.io/docs/entry/howto/mackerel-agent-plugins).

Once the official plugin pack has finished installing we will append the following configuration to `/etc/mackerel-agent/mackerel-agent.conf`.

```toml
[plugin.metrics.docker]
command = ["mackerel-plugin-docker", "-name-format", "name"]
```

By doing this, the CPU%, memory consumption, IO usage (IOPS, bytes transferred and queue length) of the Docker container running on that host will be graphed.

When starting up the container anew, an entry will automatically be appended to the graph. If you close the container, that entry will automatically become unviewable after a few hours.

In mackerel-plugin-docker, each container’s metrics are configured in `-name-format`. With `name` configured in the above example, each container’s Name will be used. There are a few ways to configure for Name, but typically the `--name` option of `docker run` is used. ( https://docs.docker.com/reference/cli/docker/container/run/#name )。

- -name-format
  - name .. Name of container
  - name_id .. Name of container + '_' + the first six characters of the ID
  - id .. Container’s ID
  - image .. Container’s image Name
  - image_id .. Container’s image Name + '_' + the first six characters of the ID
  - image_name .. Container’s image Name + '_' + Name of container
  - label .. Configured label( https://docs.docker.com/reference/dockerfile/#label )

If `label` is configured, you can choose which key to use in the `-label` option. 

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161045_original.png:image=http://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161045_original.png]
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161041_original.png:image=http://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161041_original.png]

Additionally, in `-method` option, the metric information can also be collected through the file system and not the API. In that case, configure with `-method File`. However, be aware that it is deprecated. 

## mackerel-agent’s Docker image

We’re offering mackerel-agent’s Docker image which you can access with the link below.

https://hub.docker.com/r/mackerel/mackerel-agent/

By using this it’s possible to start up the mackerel-agent as a container and monitor hosts and other containers as shown in the diagram below.

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161407.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161407.png]

## Starting up mackerel-agent as a container

To start up a container from the mackerel-agent image, we will execute the following command.
`<APIKEY>` will be replaced with the API key of the affiliated organization. `enable_docker_plugin` is a flag to set whether or not mackerel-plugin-docker should be used. `auto_retirement` is a flag to set whether or not the server from Mackerel should automatically retire when the container is closed, though the default setting is “off”. `opts` is the parameter transferred to mackerel-agent. `--name` is the container name. Please update these options as necessary. 

```
docker run -h `hostname` \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/mackerel-agent/:/var/lib/mackerel-agent/ \
  -e 'apikey=<APIKEY>' \
  -e 'enable_docker_plugin=1' \
  -e 'auto_retirement=0' \
  -e 'opts=-v' \
  --name mackerel-agent \
  -d \
  mackerel/mackerel-agent
```

### Monitoring the processes of other containers

To monitor the processes of other containers we will use Docker’s `link` option.
By using the `link` option we can obtain other containers’ IP addresses and port numbers through environment variables.
The prefix of each environment variable will be `<name>_PORT_<port>_<protocol>` and so by appending ADDR, PORT, or PROTO to the prefix, the IP address, port number, and protocol can be obtained (e.g. `prefix_ADDR`, `prefix_PORT`, `prefix_PROTO`).

For details on the `link` option please refer to Docker’s documentation.
( https://docs.docker.com/engine/network/links/ )


Here we will show an example with memcached.

First we will start up the memcached container with the name memcached.

```
docker run -d -P \
  --name memcached -p 11211:11211 \
  sylvainlasnier/memcached
```

Next we will prepare the settings file of Mackerel’s memcached plugin.
This file will be kept on the host side. Through Docker’s `link` option, we can obtain the container’s IP address with the `MEMCACHED_PORT_11211_TCP_ADDR` environment variable.

```
% cat /etc/mackerel-agent/conf.d/memcached.conf
[plugin.metrics.memcached]
command = "mackerel-plugin-memcached -host=$MEMCACHED_PORT_11211_TCP_ADDR"
```

Lastly we will start up the mackerel-agent container that was linked to this memcached container. 
Here we’ll use the `-v` option and `include` and the settings file from above will be read.

```
docker run -h `hostname` \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/mackerel-agent/:/var/lib/mackerel-agent/ \
  -e 'apikey=<APIKEY>' \
  -e 'enable_docker_plugin=1' \
  -e 'auto_retirement=0' \
  -e 'opts=-v' \
  --link memcached:memcached \
  -v /etc/mackerel-agent/conf.d:/etc/mackerel-agent/conf.d:ro \
  -e 'include=/etc/mackerel-agent/conf.d/*.conf' \
  --name mackerel-agent \
  -d \
  mackerel/mackerel-agent
```

With this you will be able to start up the mackerel-agent container that will monitor the memcached container.
