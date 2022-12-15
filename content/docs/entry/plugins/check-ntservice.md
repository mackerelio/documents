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
| --exclude-service | -E     |          | Exclude from monitoring if the specified string is contained |
| --list-service    | -l     |          | Displays a list of service names                             |

- Multiple specification of options and specification by regular expressions are not supported.

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

In such cases, use the `--exclude-service` option in conjunction with the `--exclude-service` option to exclude services that you do not want to target.

For example, if monitoring a service called `foo` unintentionally detects another service called `foobar`, configure as follows.

```
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "foo", "--exclude-service", "foobar"]
```

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-ntservice

