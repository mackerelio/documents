---
Title: チェックプラグイン一覧
Date: 2023-01-13T15:33:28+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-plugins-list
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889953808147
CustomPath: plugins/check-plugins-list
---

チェックプラグインの利用環境ごとの対応一覧です。[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) に記載の手順でプラグインのインストールを行うと、○ の付いているプラグインが利用可能になります。

container-agent に関してはプラグインを同梱した Docker イメージを公開しています。詳しくは [コンテナを監視する](https://mackerel.io/ja/docs/entry/howto/container-agent) を参照してください。

ヘルプは順次公開予定です。ヘルプが未公開のプラグインの使い方については README を参照してください。

| プラグイン                              | ドキュメント                                                                                                                                                                | Linux | Windows | container-agent    |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----- | ------- | --- |
| check-aws-cloudwatch-logs          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-cloudwatch-logs)                                                                  | ○     |         |     |
| check-aws-cloudwatch-logs-insights | [README](https://github.com/mackerelio/check-aws-cloudwatch-logs-insights)                                                                                      |       |         |     |
| check-aws-sqs-queue-size           | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-sqs-queue-size)                                                                   | ○     |         |     |
| check-cert-file                    | [README ](https://github.com/mackerelio/go-check-plugins/tree/master/check-cert-file)                                                                           | ○     |         | ○    |
| check-disk                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-disk)                                                                                 | ○     | ○       |     |
| check-elasticsearch                | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-elasticsearch)                                                                        | ○     |         | ○    |
| check-file-age                     | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-file-age)                                                                             | ○     |         | ○    |
| check-file-size                    | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-file-size)                                                                            | ○     |         | ○    |
| check-http                         | [ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-http)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-http)                         | ○     |         | ○    |
| check-jmx-jolokia                  | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-jmx-jolokia)                                                                          | ○     |         | ○    |
| check-ldap                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/caheck-ldap)                                                                                 | ○     |         |     |
| check-load                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-load)                                                                                 | ○     |         |     |
| check-log                          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-log)                                                                                  | ○     | ○       | ○    |
| check-mailq                        | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-mailq)                                                                                | ○     |         |     |
| check-masterha                     | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-masterha)                                                                             | ○     |         |     |
| check-memcached                    | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-memcached)                                                                            | ○     |         | ○    |
| check-mysql                        | [ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-mysql)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-mysql)                       | ○     |         | ○    |
| check-ntpoffset                    | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ntpoffset)                                                                            | ○     |         |     |
| check-ntservice                    | [ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-ntservice)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ntservice)               | ○     | ○       |     |
| check-ping                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ping)                                                                                 | ○     |         |     |
| check-postgresql                   | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-postgresql)                                                                           | ○     |         | ○    |
| check-procs                        | [ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-procs)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-procs)                       | ○     | ○       |     |
| check-redis                        | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-redis)                                                                                | ○     |         | ○    |
| check-smtp                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-smtp)                                                                                 | ○     |         |     |
| check-solr                         | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-solr)                                                                                 | ○     |         |     |
| check-ssh                          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ssh)                                                                                  | ○     |         | ○    |
| check-ssl-cert                     | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-ssl-cert)                                                                             | ○     |         | ○    |
| check-tcp                          | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-tcp)                                                                                  | ○     | ○       | ○    |
| check-uptime                       | [README](https://github.com/mackerelio/go-check-plugins/tree/master/check-uptime)                                                                               | ○     | ○       |     |
| check-windows-eventlog             | [ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-windows-eventlog)<br>[README](https://github.com/mackerelio/go-check-plugins/tree/master/check-windows-eventlog) | ○     | ○       |     |
