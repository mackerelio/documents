---
Title: Setting up mackerel-container-agent in Amazon ECS
Date: 2019-05-20T15:49:56+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/container/ecsv3
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17680117127150854409
CustomPath: howto/install-agent/container/ecsv3
---

This is the setup process of mackerel-container-agent in Amazon ECS.

## Notes

This setup process is deprecated for mackerel-container-agent v0.1.0 or later.
Please see [Setting up mackerel-container-agent on Amazon ECS](https://mackerel.io/docs/entry/howto/install-agent/container/ecs).

## Important points regarding billing

In Mackerel, one task is registered as one host. Please note that charges will incur for paid plans. For more details, refer to [FAQ・Calculating the number of hosts](https://mackerel.io/docs/entry/faq/contracts/calculate-host-number).

## Add containers to the task definitions

Add the mackerel-container-agent container to the task definition that you want to monitor.
Add the mackerel-container-agent with the following configuration from the task definition's 'Add container' option.

| Item | Value |
| :-- | :-- |
| container name| mackerel-container-agent |
| image|  mackerel/mackerel-container-agent:latest |
| memory limit|  hard limit: 128 |
| environment variable (key: value) | MACKEREL_CONTAINER_PLATFORM: "ecs_v3"<br>MACKEREL_APIKEY: Mackerel API key |

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
            Value: ecs_v3
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
