---
Title: Setting up mackerel-container-agent for AWS Fargate, Amazon ECS(awsvpc network mode)
Date: 2019-02-07T17:47:13+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/container/ecsawsvpc
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/98012380860620427
---

This document details the setup process of mackerel-container-agent for AWS Fargate, Amazon ECS(awsvpc network mode).

If using **a network mode other than awsvpc** with ECS, refer [here](https://mackerel.io/docs/entry/howto/install-agent/container/ecs).

## Notes

This setup process is deprecated for mackerel-container-agent v0.1.0 or later.
Please see [Setting up mackerel-container-agent on Amazon ECS](https://mackerel.io/docs/entry/howto/install-agent/container/ecs).

## Point to note regarding billing

For every one task, a micro host will be registered in Mackerel. Please note that charges will occur if using a paid plan. For more details, refer to [FAQ · Calculating the number of hosts](https://mackerel.io/docs/entry/faq/contracts/calculate-host-number).

## Adding the container in the task definition

Add the mackerel-container-agent container in the task definition that you would like to monitor.
Add mackerel-container-agent with the following configuration from the "Add Container" option in the task definition.

| Item | Value |
| :-- | :-- |
| Container name| mackerel-container-agent |
| Image|  mackerel/mackerel-container-agent:latest |
| Memory limit|  Hard limit: 128 |
| Environment variable (key:value) | MACKEREL_CONTAINER_PLATFORM: "ecs_awsvpc" (For ECS(awsvpc network mode)) or "fargate" (For Fargate)<br>MACKEREL_APIKEY: Mackerel API key |

When using roles or plugins, refer to the "Agent congifurations" [here](https://mackerel.io/docs/entry/howto/container-agent).

### CloudFormation template example (YAML)

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
            Value: fargate
          - Name: MACKEREL_APIKEY
            Value: <YOUR_MACKEREL_APIKEY>
        Essential: false
```

## Start monitoring

Execute the task with mackerel-container-agent added and start monitoring.

If this does not work, please refer to the task log.
