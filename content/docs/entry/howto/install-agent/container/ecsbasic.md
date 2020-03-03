---
Title: Setting up mackerel-container-agent on Amazon ECS(default, bridge, host, none
  network mode)
Date: 2019-06-17T11:32:15+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/container/ecsbasic
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17680117127201755374
---

This document details the setup process for mackerel-container-agent on Amazon ECS(default, bridge, host, none network mode).

## Notes

This setup process is deprecated for mackerel-container-agent v0.1.0 or later.
Please see [Setting up mackerel-container-agent on Amazon ECS](https://mackerel.io/docs/entry/howto/install-agent/container/ecs).

## Point to note regarding billing

For every one task, a micro host will be registered in Mackerel. Please note that charges will occur if using a paid plan. For more details, refer to [FAQ · Calculating the number of hosts](https://mackerel.io/docs/entry/faq/contracts/calculate-host-number).

## Supported network modes

In this process, the following ECS network modes are applicable.

- default
- bridge
- host
- none

When using the **awsvpc network mode**, please refer to [Setting up mackerel-container-agent on Fargate, ECS (awsvpc network mode)](https://mackerel.io/docs/entry/howto/install-agent/container/ecsawsvpc).

## Operation conditions

The user within the mackerel-container-agent container must be the root(uid=0).
Please take this into account when changing the user of the container.

## Adding the container in the task definition

Add the mackerel-container-agent container in the task definition that you would like to monitor.

### Adding Volume

Add volume to be used with mackerel-container-agent.
Add the following volume with the "Add volume" option of the task definition.

| Name | Source path |
| :-- | :-- |
| cgroup | /cgroup (For AL2: /sys/fs/cgroup) |
| docker_sock | /var/run/docker.sock |

## Adding the container

Add mackerel-container-agent with the following configuration from the "Add Container" option of the task definition.

| Item | Value |
| :-- | :-- |
| Container name| mackerel-container-agent |
| Image|  mackerel/mackerel-container-agent:latest |
| Memory limit|  Hard limit: 128 |
| Mount point<br>(Source volume: Container path)| Check both cgroup: /host/sys/fs/cgroup and <br>docker_sock: /var/run/docker.sock<br> as read only |
| Environment variable (key:value) | MACKEREL_CONTAINER_PLATFORM: ecs<br>MACKEREL_APIKEY: Mackerel API key |

When using roles and plugins, refer to the Agent configurations [here](https://mackerel.io/docs/entry/howto/container-agent).

### CloudFormation template example (YAML)

```
TaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Volumes:
      - Name: cgroup
        Host:
          SourcePath: /cgroup
      - Name: docker_sock
        Host:
          SourcePath: /var/run/docker.sock
    ContainerDefinitions:
      - Name: alpine
        Image: alpine:latest
        Memory: 64
        EntryPoint:
          - sh
          - -c
        Command:
          - "/bin/sh -c \"while true; do sleep 1; done\""
        Essential: true
      - Name: mackerel-container-agent
        Image: mackerel/mackerel-container-agent:latest
        Memory: 128
        MountPoints:
          - SourceVolume: cgroup
            ContainerPath: /host/sys/fs/cgroup
            ReadOnly: true
          - SourceVolume: docker_sock
            ContainerPath: /var/run/docker.sock
            ReadOnly: true
        Environment:
          - Name: MACKEREL_CONTAINER_PLATFORM
            Value: ecs
          - Name: MACKEREL_APIKEY
            Value: <YOUR_MACKEREL_APIKEY>
        Essential: false
```

## Start monitoring

Execute the task with mackerel-container-agent added and start monitoring.

If this does not work, please refer to the task log.
