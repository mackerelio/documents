---
Title: Check plugins list
Date: 2023-01-13T15:32:46+09:00
URL: https://mackerel.io/docs/entry/plugins/check-plugins-list
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889953807916
CustomPath: plugins/check-plugins-list
---

List of check plugins supported by each environment of use. Plugins marked with checkmark will be available after installing the plugin according to the procedure described in [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins) .

For container-agent, we have released a Docker image that includes plugins. See [Monitoring Containers](https://mackerel.io/docs/entry/howto/container-agent) for more information. 

Help will be released as it becomes available. Please refer to the README for information on how to use plugins for which help is not yet available.

| Plugins                              | Documents                                                                                                                                                                | Linux | Windows | container-agent    |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----- | ------- | --- |
| check-aws-cloudwatch-logs          | [Help](https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-cloudwatch-logs)                                                                  | ✓     |         |     |
| check-aws-cloudwatch-logs-insights | [Help](https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs-insights)<br>[README](https://github.com/mackerelio/check-aws-cloudwatch-logs-insights)                                                                                      |       |         |     |
| check-aws-sqs-queue-size           | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-sqs-queue-size)                                                                   | ✓     |         |     |
| check-cert-file                    | [README ](https://github.com/mackerelio/go-check-plugins/tree/master/check-cert-file)                                                                           | ✓     |         | ✓    |
| check-disk                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-disk)                                                                                 | ✓     | ✓       |     |
| check-dns                          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-dns)                                                                                  | ✓     |         |      |
| check-elasticsearch                | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-elasticsearch)                                                                        | ✓     |         | ✓    |
| check-file-age                     | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-file-age)                                                                             | ✓     |         | ✓    |
| check-file-size                    | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-file-size)                                                                            | ✓     |         | ✓    |
| check-http                         | [Help](https://mackerel.io/docs/entry/plugins/check-http)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-http)                         | ✓     |         | ✓    |
| check-jmx-jolokia                  | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-jmx-jolokia)                                                                          | ✓     |         | ✓    |
| check-ldap                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/caheck-ldap)                                                                                 | ✓     |         |     |
| check-load                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-load)                                                                                 | ✓     |         |     |
| check-log                          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-log)                                                                                  | ✓     | ✓       | ✓    |
| check-mailq                        | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-mailq)                                                                                | ✓     |         |     |
| check-masterha                     | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-masterha)                                                                             | ✓     |         |     |
| check-memcached                    | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-memcached)                                                                            | ✓     |         | ✓    |
| check-mysql                        | [Help](https://mackerel.io/docs/entry/plugins/check-mysql)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-mysql)                       | ✓     |         | ✓    |
| check-ntpoffset                    | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ntpoffset)                                                                            | ✓     |         |     |
| check-ntservice                    | [Help](https://mackerel.io/docs/entry/plugins/check-ntservice)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ntservice)               | ✓     | ✓       |     |
| check-ping                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ping)                                                                                 | ✓     |         |     |
| check-postgresql                   | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-postgresql)                                                                           | ✓     |         | ✓    |
| check-procs                        | [Help](https://mackerel.io/docs/entry/plugins/check-procs)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-procs)                       | ✓     | ✓       |     |
| check-redis                        | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-redis)                                                                                | ✓     |         | ✓    |
| check-smtp                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-smtp)                                                                                 | ✓     |         |     |
| check-solr                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-solr)                                                                                 | ✓     |         |     |
| check-ssh                          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ssh)                                                                                  | ✓     |         | ✓    |
| check-ssl-cert                     | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ssl-cert)                                                                             | ✓     |         | ✓    |
| check-tcp                          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-tcp)                                                                                  | ✓     | ✓       | ✓    |
| check-uptime                       | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-uptime)                                                                               | ✓     | ✓       |     |
| check-windows-eventlog             | [Help](https://mackerel.io/docs/entry/plugins/check-windows-eventlog)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-windows-eventlog) | ✓     | ✓       |     |
