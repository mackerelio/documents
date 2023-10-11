---
Title: Check plugins - check-ntservice
Date: 2022-12-15T16:23:56+09:00
URL: https://mackerel.io/docs/entry/plugins/check-ntservice
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889945319222
CustomPath: plugins/check-ntservice
---

check-ntservice is a plugin that monitors whether Windows services are running.


[:contents]

<h2 id="options">Configurable options</h2>

| Option            | Short  | Required | Description                                                  |
| ----------------- | ------ | -------- | ------------------------------------------------------------ |
| --service-name    | -s     | ✓        | Monitor services that contain the specified string           |
| --exclude-service | -x     |          | Exclude from monitoring if the specified string is contained |
| --list-service    | -l     |          | Displays a list of service names                             |
| --exact           |        |          | Check the service name for exact match                       |

- Multiple specification of options and specification by regular expressions are not supported.
- `--exact` has been enabled for plugins shipped with mackerel-agent v0.78.0 and later.

<h2 id="config">Example configuration</h2>

To monitor the availability of the W3SVC service, configure as follows.

```
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "W3SVC"]
```

To check the above settings manually, you can run the following at the command prompt.

- The path to the command must be included in the PATH system environment variable.

```
check-ntservice --service-name W3SVC
```

<h2 id="tips">Tips</h2>

- You can confirm the service name in advance by executing the command with the `--list-service` option.

```
check-ntservice --list-service
```

<h2 id="troubleshoot">Troubleshooting</h2>

### Unintended service outages are detected.

Because check-ntservice checks for services containing the string specified in the `--service-name` option, it may detect unintended service outages.

In this case, use the `--exact` or `--exclude-service` option to identify the target service or exclude services that contain unnecessary strings.

For example, if you have two services running, `foo` and `foobar`, and you want to monitor `foo`, you can simply specify the `--exact` option as follows.

```
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "foo", "--exact"]
```

It may also be excluded by specifying the `--exclude-service` option as follows.

```
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "foo", "--exclude-service", "foobar"]
```

### When the specified service is not found, an UNKNOWN alert is emitted.

An UNKNOWN alert with the message `service does not exist.` is caused when a service specified in the condition is not found.

Please check whether the correct service name is specified or not by utilizing the `--list-service` option.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-ntservice

