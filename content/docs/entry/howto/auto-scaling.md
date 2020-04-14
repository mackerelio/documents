---
Title: Using Mackerel in Auto Scaling environments
Date: 2015-10-23T18:22:54+09:00
URL: https://mackerel.io/docs/entry/howto/auto-scaling
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415125592473
---

Autoscaling structures such as [AWS Auto Scaling](https://aws.amazon.com/jp/autoscaling/) and [Heroku](https://www.heroku.com/) which automatically increase and decrease the number of servers in response to load are gradually becoming more widely used. Here we’d like to introduce and talk about some ways Mackerel can be best used in Auto Scaling environments. For those who are using Heroku, be sure to refer to our guide [Monitoring Heroku with Mackerel](https://mackerel.io/docs/entry/advanced/monitoring-heroku) as well.

[:contents]

# Notes on auto retirement processing

The auto retirement process of mackerel-agent detects the closing of OS for various environments and sends a request to Mackerel as a notification of retirement. If the retirement request fails, mackerel-agent will retry, but because this is a process executed within the OS shutdown, situations such as the OS shutdown being completed before the request succeeds may occur.

Therefore, please be aware that mackerel-agent auto retirement processing may not succeed depending on execution environment compatibility etc.

# mackerel-agent config for Auto Scaling environments

First, we’ll introduce the recommended configurations for mackerel-agent in Auto Scaling environments.


## Setting the host status to `working` upon startup

For hosts running in Auto Scaling environments, we recommend configuring the status to automatically be set to `working` upon startup so that they begin monitoring and sending notifications.
This can be done by specifying the following settings in the mackerel-agent configuration file.

```
# /etc/mackerel-agent/mackerel-agent.conf
[host_status]
on_start = "working"
```

Even without assigning the above configurations, if the host's default status is set to `working` in your organization's settings, the same result will be achieved.
For more information please refer to [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts)

## Automatic host retirement when scaling down

In Mackerel, a connectivity alert is generated if a host is suspended and mackerel-agent terminates.
This is beneficial for detecting sudden interruptions for regular hosts.

However, host suspension isn’t considered abnormal behavior in Auto Scaling environments. So, let's make the arrangements so that when a host is suspended, it is retired accordingly in Mackerel and alerts are not generated.
Depending on how mackerel-agent is launched and the OS used, there are three different ways to do this. Both methods are described below, so please follow the one that fits your situation.

### Installing with rpm/deb

For those using package install, automatic host retirement can be set up simply by appending one line into the environment variable configuration file.

In the environment variable configuration file, located at /etc/sysconfig/mackerel-agent for yum/rpm, and /etc/default/mackerel-agent for deb/apt, append the following line.

```
AUTO_RETIREMENT=1
```

By making this specification, hosts will automatically retire upon shutdown (For example `/etc/init.d/mackerel-agent stop`).

**Caution** Even if `/etc/init.d/mackerel-agent stop` is entered in manually, if this specification is made, the host will be retired. Please use `/etc/init.d/mackerel-agent reload` to stop/start the agent when updating the version, etc.

For more information, please refer to the [mackerel-agent specs](https://mackerel.io/docs/entry/spec/agent) as well as [“Auto host retirement and status change; automation for cold standby systems and auto scale”](https://mackerel.io/blog/entry/2015/08/03/142244). 

### Launching with shell script

For those not using package install, or for those who are using package install but launching with a unique shell script rather than via init script, here's how to set up automatic host retirement when the shell script terminates.

Setting up automatic host retirement can be carried out by using the retire subcommand `mackerel-agent retire [-force]` and the trap command. 
Now, insert the retire command as a signal banner using the trap command into the shell script that launches mackerel-agent as shown below.

```
trap 'mackerel-agent retire -conf /etc/mackerel-agent/mackerel-agent.conf -force' INT TERM
mackerel-agent -conf /etc/mackerel-agent/mackerel-agent.conf
```

As a result, host retirement will be handled automatically and terminated when SIGINT or SIGTERM is sent. So, host registration / retirement processing will be automatically carried out according to the increase and decrease of hosts in heroku or AWS Auto scaling environments.

### Using the Windows version mackerel-agent

If using mackerel-agent on Windows, set the following value in the system environment variable.

```
MACKEREL_AUTO_RETIREMENT=1
```

You can configure this using the GUI or by executing the following command in the command prompt or PowerShell.

```
setx MACKEREL_AUTO_RETIREMENT 1 -m
```

### Registering EC2 using AWS Integration

If registering an [EC2](https://mackerel.io/docs/entry/integrations/aws/ec2) host using [AWS Integration](https://mackerel.io/docs/entry/integrations/aws), it can be automatically retired through AWS Integration.

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200413/20200413124517.png:image]

When this function is enabled, the retirement process will automatically execute when the instance terminates.
This function can be used in conjunction with the auto retirement function of mackerel-agent described above. This is an effective way to ensure that auto retirement is performed.


## Viewing graphs for Auto Scaling

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150730/20150730191150_original.png:image]

In Mackerel, hosts that are added, removed, or replaced in an Auto Scaling environment, including past hosts, will be displayed in role graphs as shown in the example above.
Role graphs are graphs that display hosts grouped by role in each service screen.

Even in Auto Scaling environments, easy to view graphs can be automatically generated by assigning roles, so we hope you’ll give assigning services and roles a try.
For more concrete information about service and role configurations please refer to the [mackerel-agent specs](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles).

# Points to note when creating Machine Images installed with mackerel-agent
 
In Mackerel, in order to uniquely identify hosts, each one is issued a "Host ID" and is managed in association to it ([reference](https://mackerel.io/docs/entry/spec/agent#faq)). ID information is saved in the "var/lib/mackerel-agent/id" file for Linux OS hosts and in the "id" file inside the installation folder for Windows OS.

When creating a custom Machine Image installed with mackerel-agent as the start-up instance’s base image when scale-out with Auto Scaling occurs, make sure that the image does not contain the file with this host ID. (If a scale-out occurs and the ID file has not been deleted, Mackerel will identify all started servers as the same.)

