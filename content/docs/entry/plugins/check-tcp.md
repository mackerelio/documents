---
Title: Check plugins - check-tcp
Date: 2023-01-27T09:09:06+09:00
URL: https://mackerel.io/docs/entry/plugins/check-tcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889957873802
CustomPath: plugins/check-tcp
---

check-tcp is a plug-in that monitors connections to servers via TCP.

[:contents]

<h2 id="options">Configurable options</h2>

| Option                 | Short  | Description                                                                                                                                                 | Default value |
| ---------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| --service              |        | Specify a service name. see [Supported services](#Supported services)                                                                                       |               |
| --hostname             | -H     | Host name or IP address to check for communication                                                                                                          |               |
| --port                 | -p     | Port number to check for communication                                                                                                                      |               |
| --send                 | -s     | String to be sent to server                                                                                                                                 |               |
| --expect-pattern       | -e     | Regular expression patterns expected in responses from the server                                                                                           |               |
| --quit                 | -q     | String to be sent when disconnecting from the server                                                                                                        |               |
| --ssl                  | -S     | TLS connection required or not                                                                                                                              | false         |
| --unix-sock            | -U     | Unix socket                                                                                                                                                 |               |
| --no-check-certificate |        | Whether or not certificate verification is required                                                                                                         | false         |
| --timeout              | -t     | Connection timeout time (sec)                                                                                                                               | 10            |
| --maxbytes             | -m     | Disconnects when more than the specified number of bytes of data is received                                                                                |               |
| --delay                | -d     | Latency between sending data and polling for a response (sec)                                                                                               |               |
| --warning              | -w     | Response time as Warning (sec)                                                                                                                              |               |
| --critical             | -c     | Response time as Critical (sec)                                                                                                                             |               |
| --escape               | -E     | Escape if the string specified by `--quit` contains `\n` `\r` `\t` `\c`. If not specified, add `\r\n` at the end of the string specified by `--quit`.       | false         |
| --error-warning        | -W     | Set the alert status to WARNING when the plugin terminates abnormally                                                                                       | CRITICAL      |
| --expect-closed        | -C     | Verify that the port/unixsock is closed. If the port/unixsock is closed, OK status returned; if open, Critical or Warning status is returned depending on the value of the `--error-warning`. This option only verifies the connection. | false         |

<h2 id="services">Supported services</h2>

If you specify the corresponding service name in the `--service` option, the check will be performed for each corresponding option.

| Service name | port | send      | expect-pattern         | quit        | ssl |
| ------------ | ---- | --------- | ---------------------- | ----------- | --- |
| FTP          | 21   |           | `^220`                 | `QUIT`      |     |
| POP          | 110  |           | `^\+OK`                | `QUIT`      |     |
| SPOP         | 995  |           | `^\+OK`                | `QUIT`      | ✓   |
| IMAP         | 143  |           | `^\* OK`               | `a1 LOGOUT` |     |
| SIMAP        | 993  |           | `^\* OK`               | `a1 LOGOUT` | ✓   |
| SMTP         | 25   |           | `^220`                 | `QUIT`      |     |
| SSMTP        | 465  |           | `^220`                 | `QUIT`      | ✓   |
| GEARMAN      | 7003 | version\n | `\A[0-9]+\.[0-9]+\n\z` |             |     |

<h2 id="config">Example configuration</h2>

The following is a configuration that checks for connections on port `4224` of `localhost`, and if the connection takes more than 3 seconds, it is set to Warning, and if it takes more than 5 seconds, it is set to Critical.

```
[plugin.checks.check-tcp-sample]
command = ["check-tcp", "-H", "localhost", "-p", "4224", "-w", "3", "-c", "5"]
```

To verify the above settings by executing directly from a terminal or other means, execute as follows.

```
check-tcp -H localhost -p 4224 -w 3 -c 5
```

To specify an IPv6 address, enclose it in single-byte brackets `[]` like `[::1]`.

```
[plugin.checks.check-tcp-v6-sample]
command = ["check-tcp", "-H", "[::1]", "-p", "4224", "-w", "3", "-c", "5"]
```

<h2 id="tips">Tips</h2>

The check-tcp plugin can check the correctness of simple message exchanges during connection checking.

The `--service` option checks messages according to the supported protocols, but you can also use `--send` or `--expect-pattern` to monitor your own protocols.

For example, to check that the reason phrase in the HTTP response status line is the expected message, the following configuration can be used.

```
[plugin.checks.tcp_http404]
command = ["check-tcp", "-H", "localhost", "-p", "80", "-s", "GET / HTTP/1.1\\r\\n\\r\\n", "-e", "HTTP/1.1 404 Bad Request", "-E"]
```

To check the operation in advance, perform the following.

```
check-tcp -H localhost -p 80 -s "GET / HTTP/1.1\r\n\r\n" -e "HTTP/1.1 404 Bad Request" -E
```
<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-tcp

