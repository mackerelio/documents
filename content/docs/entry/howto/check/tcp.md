---
Title: Running checks on TCP servers
Date: 2015-12-02T14:56:04+09:00
URL: https://mackerel.io/docs/entry/howto/check/tcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653586347147184061
---

Using `check-tcp` from the official check-plugin pack, you can monitor TCP servers with Mackerel. For installing the official check-plugin pack, please refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins).

## Checking HTTP servers

Below are configurations for an HTTP application server that will send the request `GET / HTTP/1.0\r\n\r\n` and check to see if "OK Farm" is contained in the response. By assigning the option `--escape`, characters such as "\r" and "\n" contained in the argument of `--send` will be treated as newline characters.

Additionally, it it is possible to configure thresholds for how many seconds it takes to connect, and with the example below, a Warning alert will be created if connection takes more than 3 seconds, and a Critical alert if it takes more than 10 seconds.

```config
[plugin.checks.tcp_app]
command = ["check-tcp", "--hostname", "localhost", "--port", "5000", "--send", "GET / HTTP/1.0\r\n\r\n", "--escape", "--expect-pattern", "OK Farm", "--warning", "3", "--critical", "10"]
```

## Using with an assigned service

Of course it's also possible to check servers that aren't HTTP servers. By assigning the `--service` option for several services, you can run standard checks without doing any complicated configurations. For example FTP would be configured as shown below.

```config
[plugin.checks.ftp]
command = ["check-tcp", "--service", "ftp", "-H", "localhost"]
```

This is equivalent to the configuration below.

```config
[plugin.checks.ftp]
command = ["check-tcp", "-H", "localhost", "--port", "21", "--expect-pattern", "^200", "--quit", "QUIT"]
```

Possible settings that can be assigned to `--service` are: FTP, POP, SPOP, IMAP, SIMAP, SMPT, and SSMTP.

For information regarding other options please refer to `check-tcp --help` and the [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-tcp/README.md).

## Source code

The source code for `check-tcp` is publicly available via the URL below.

<https://github.com/mackerelio/go-check-plugins/tree/master/check-tcp>
