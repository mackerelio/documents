---
Title: Monitoring Processes
Date: 2015-11-06T16:25:48+09:00
URL: https://mackerel.io/docs/entry/howto/check/process
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415127142653
---

Using `check-procs` from the official check plugins pack, you can monitor processes. For information on installing the official check plugin pack, you can refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins).

## Example: monitoring the cron process

To monitor cron, we will first append the following item in mackerel-agent.conf and then restart the mackerel-agent.

```config
[plugin.checks.check_cron]
command = ["check-procs", "--pattern", "crond"]
```

In the `--pattern` option we will assign a regular expression that will match to the target process. With this arrangement, if crond suspends operations an alert will be opened, and when the process is restored the alert will close automatically.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151105/20151105163711.png)

## Monitoring the number of processes

`check-procs` has the following available options, and it’s possible to configure thresholds for the number of processes. This is convenient when you want to monitor the number of worker processes.

- `-w`, `--warn-over`
  - warning alert will be opened if the configured value is exceeded
- `-c`, `--critical-over`
  - critical alert will be opened if the configured value is exceeded
- `-W`, `--warn-under`
  - warning alert will be opened if the processes drop below the configured value
- `-C`, `--critical-under`
  - critical alert will be opened if the processes drop below the configured value

The following configurations would be made, for example, when you want to monitor with the number of Nginx workers included.

    [plugin.checks.check_nginx_worker]
    command = ["check-procs", "-p", "nginx", "-W", "8", "-w", "10", "-C", "1", "-c", "30", "--user", "nginx"]

In this example, if the number of Nginx workers is less than 8 or more than 10, a Warning alert will be raised. If the number is less than 1 or greater than 30, a Critical alert will be generated. We have designated the executive user with the `--user` option and made configurations to more accurately collect the number of workers.

There are additional options for `check-procs` such as for monitoring the execution time and the status of processes. For more information please refer to `check-procs --help` and the [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-procs/README.md).

## Source code

The source code for `check-procs` is publicly available via the URL below.

<https://github.com/mackerelio/go-check-plugins/tree/master/check-procs>
