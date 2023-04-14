---
Title: Check plugins - check-mysql
Date: 2023-04-14T12:03:17+09:00
URL: https://mackerel.io/docs/entry/plugins/check-mysql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889981070025
CustomPath: plugins/check-mysql
---

check-mysql is a check plugin for monitoring MySQL. It can perform check monitoring based on the conditions of the subcommands used.

[:contents]

## Subcommand

The available subcommands are as follows.

| Command     | Monitoring                            | Monitoring Target                                                                           | Note                                                                                                                                        |
| ----------- | ------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| uptime      | MySQL Server uptime                   | uptime value of `SHOW GLOBAL STATUS`                                                   |                                                                                                                                             |
| readonly    | Whether MySQL server is set to read-only | read_only value of `SHOW GLOBAL VARIABLES`                                             |                                                                                                                                             |
| replication | Replication status of MySQL server    | - Whether IO_Running and SQL_Running in `SHOW REPLICA STATUS` are both set to Yes<br>- Whether the value of Seconds_Behind over the threshold | - REPLICATION CLIENT privilege is required for the connecting user<br>- In version before MySQL 8.0.22, use `SHOW SLAVE STATUS`. |
| connection  | Number of MySQL server connections    | Threads_connected value of `SHOW GLOBAL STATUS`                                        |                                                                                                                                             |

## Configurable options

The following options can be specified for all subcommands.

| Option          | Short | Description                                                    | Default   |
| --------------- | ----- | -------------------------------------------------------------- | --------- |
| --host           | -H    | Target host                                                    | localhost |
| --port           | -p    | Connection port                                                | 3306      |
| --socket         | -S    | Socket file                                                    |           |
| --user           | -u    | User name                                                      | root      |
| --password       | -P    | Password                                                       |           |
| --tls             |       | Enable TLS connection                                          |           |
| --tls-root-cert  |       | Specify the root certificate to be used for the TLS connection |           |
| --tls-skip-verify |       | Do not validate certificates during TLS connections            |           |
| --help            | -h    | Show help<br>ex. `check-mysql connection -h`                   |           |

### Threshold Option

The following options for threshold values have different conditions depending on the subcommand. If you don't specify the option, it defaults to the threshold.

| Subcommand     | Option                     | Short      | Description                             | Default                           |
| ----------- | ------------------------- | -------- | ------------------------------ | -------------------------------- |
| uptime      | --critical<br>--warning | -c<br>-w | If Uptime is less than the specified number of seconds           | 0 critical<br>0 warning     |
| readonly    | none                        |          |                                |                                  |
| replication | --critical<br>--warning | -c<br>-w | If Seconds_Behind exceeds the specified number of seconds   | 250 critical<br>200 warning |
| connection  | --critical<br>--warning | -c<br>-w | If Threads_Connected exceeds the specified number | 250 critical<br>200 warning |


## Example configurations

The following configuration will generate a Warning alert when the number of MYSQL connections exceeds 250 and a Critical alert when the number exceeds 280.

```
[plugin.checks.check-mysql-sample]
command = ["check-mysql", "connection", "--host", "127.0.0.1", "--port", "3306", "--user", "USER", "--password", "PASSWORD", "--warning", "250", "--critical", "280"]
```

To check the above settings directly from a terminal, execute as follows.

```
check-mysql connection --host 127.0.0.1 --port 3306 --user USER --password PASSWORD --warning 250 --critical280
```

## Tips

### How to write MySQL connection information

If you want to avoid putting the connection information needed to run check-mysql directly in the `--password` option, you can take the following approaches. Note that these approaches require that the contents of `command` be written as a string, not an array.


**Use the command**

If you specify a string, the contents of `command` will be executed via the shell, so you can use the command to output the password.

mackerel-agent.conf
```
command = "check-mysql ... --password "<Command to output password>"
```

**Using Environment Variables**

You can define environment variables in the configuration file and optionally specify variables.

mackerel-agent.conf
```
command = "check-mysql ... --password $MYSQL_PASSWORD ・・・"
env = { "MYSQL_PASSWORD" = "xxxxx" }
```

You can also use environment variables applied to mackerel-agent processes. For details, please refer to [Applying Environment Variables](https://mackerel.io/docs/entry/spec/agent#environment-variables).

## Troubleshooting

### "Couldn't..." error message was output

There are following possibility.

* Connection information is incorrect
* The user being connected does not have permission to view MySQL information

Run check-mysql manually on the host and see if you can get any information.


## Repository

https://github.com/mackerelio/go-check-plugins/tree/master/check-mysql
