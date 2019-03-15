---
Title: Monitoring batch jobs that use cron etc. with mkr wrap
Date: 2019-02-19T12:22:14+09:00
URL: https://mackerel.io/docs/entry/howto/mkr/wrap
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17680117126970795193
---

Using `mkr wrap`, you can monitor the success or failure of a program that is executed in fixed intervals by cron etc. 

```
% mkr wrap -- /path/to/your-batch ...
```

By executing a command like the one described above, if the command fails (non-zero exit), an alert will be generated in Mackerel.

The alert is registered as a check monitoring alert of the execution host. The host ID and the API key needed to post information to Mackerel are automatically obtained from the configuration file (mackerel-agent.conf), but can be explicitly specified as well.

## Registering in crontab

For example, a batch that is executed every hour at 11 minutes would only need the following specification. 

```
11 * * * * mkr wrap -- /path/to/your-batch ...
```

## Alert status

If execution of a command fails, then an alert will occur in Mackerel as shown in the screenshot below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190219/20190219115516.png)

Here, the failed command and its contents will be displayed. For this reason, please be careful not to include any sensitive information that you wouldn't want leaked, such as passwords, in the command or its options.

## Detailed usage

As previously mentioned, `mkr wrap` can easily be used without any special specifications needed.

```
% mkr wrap [options] -- /path/to/your-batch ...
```

Write the batch job command to be executed after `--`. If needed, several options can be used before `--`.

### `-n, --name` - Monitor name

```
% mkr wrap -n your-check-monitor -- /path/to/your-batch
```

Specify the check monitoring name for the job. If this option is not specified, the name will be automatically created from the argument (something alongs the lines of "mkrwrap - {{command name}} - {{hash 6 digit value}}"), but we recommend that you explicitly specify this for the sake of clarity.

Since this is treated as a check monitor, the name must be unique to the host. Be sure not to use a name that overlaps with other check monitor names.

### `-d, --detail` - Command output transmission

By default, command output is not sent to Mackerel when sending monitoring information, but it can transmitted to and checked in Mackerel using this option. However, outputs of more than 1024 characters will be cut off.

When using this option, please be careful regarding handling sensitive information similar to the command contents.

### `-N, --note` - Notes

Specify a note. When an alert occurs, the content of this note will be included in the alert message.

### `-H, --host` - Specify the Host ID

`mkr wrap` automatically obtains the host ID from the configuration file, but if you want to specify it explicitly, use this option.

### `-w, --warning` - Set alerts to WARNING 

By default, `mkr wrap` alerts command failure as CRITICAL, but alerts can be set to WARNING by specifying this option.

### `-a, --auto-close` - Automatically close alerts upon command success

If there are previously reported alerts when a succeeding batch job of the same configuration succeeds, those alerts will automatically close. Because alerts do not auto-close by default, they must be closed manually.

This option saves the result in a temporary area and reads it at the next execution, so be sure to use it in an environment with persistent disk space.

### `-I, --notifictation-interval` - Specify the notification retransmission interval

By default, notifications are only sent when an alert occurs or when the status of an alert changes, but you can have notifications sent at fixed intervals for open alerts using this option.

The interval is specified in the form of `15m`, `1h`, `1h30m`. The shortest interval that can be specified is `10m` (10 minutes). Any specification shorter than that will be set to 10 minutes.

### `MACKEREL_APIKEY` environment variable - API key specification

Explicitly specify the API key. This can not be specified with the command line option or environments variables only.

## Points of note

Although it does not occur in most cases, obtaining the API key or host ID from the id file that stores mackerel-agent.conf and host ID may fail due to permissions.

This can only occur if you have independently set the permissions of those files as 0600 etc. This is because the execution user of the batch job is often different from the execution user of mackerel-agent.

In this case, adjust the permissions and user authorities of these files, or explicitly specify the API key and host ID in the command.

