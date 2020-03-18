---
Title: Setting up mackerel-container-agent on Amazon ECS
Date: 2019-02-07T17:46:35+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/container/ecs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/98012380860620176
---

This document details the setup process for mackerel-container-agent on Amazon ECS.

## Operating conditions

The following conditions must be met in order to run mackerel-container-agent.

- When using an EC2 launch type
   - Amazon ECS Container Agent version 1.26.1 or later
- When using a Fargate launch type
   - AWS Fargate platform version 1.3.0 or later

If you encounter a problem with the operation of mackerel-container-agent, please make sure that your environment meets these requirements.

## Point to note regarding billing

For every one task, a micro host will be registered in Mackerel. Please note that charges will occur if using a paid plan. For more details, refer to [FAQ · Calculating the number of hosts](https://mackerel.io/docs/entry/faq/contracts/calculate-host-number).

## Add containers to the task definitions

Add the mackerel-container-agent container to the task definition that you want to monitor.
Add the mackerel-container-agent with the following configuration from the task definition's 'Add container' option.

| Item | Value |
| :-- | :-- |
| container name| mackerel-container-agent |
| image|  mackerel/mackerel-container-agent:latest |
| memory limit|  hard limit: 128 |
| environment variable (key: value) | MACKEREL_CONTAINER_PLATFORM: "ecs"<br>MACKEREL_APIKEY: Mackerel API key |

cgroupfs and docker.sock mounts are no longer required.
Please see [Setting up mackerel-container-agent on Amazon ECS(default, bridge, host, none network mode)](https://mackerel.io/docs/entry/howto/install-agent/container/ecsbasic) for previous configurations.

Refer to the 'Agent configuration' section [here](https://mackerel.io/docs/entry/howto/container-agent) when using roles and plugins.

### CloudFormation template (YAML)

```
TaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    RequiresCompatibilities:
      - FARGATE
    NetworkMode: awsvpc
    Memory: 512
    Cpu: 256
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
        Environment:
          - Name: MACKEREL_CONTAINER_PLATFORM
            Value: ecs
          - Name: MACKEREL_APIKEY
            Value: <YOUR_MACKEREL_APIKEY>
        Essential: false
```

## Start monitoring

Execute the task to which mackerel-container-agent was added and start monitoring.

If this does not work, take a look at the task log.

## Limitations

If using the bridge Network Mode in the EC2 Launch Type, network interface metadata will only be collected from mackerel-container-agent containers.
Note that network interface metadata from other containers included in the task will not obtained.
