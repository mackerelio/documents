---
Title: チェックプラグイン - check-tcp
Date: 2023-01-27T09:09:07+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-tcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889957873808
CustomPath: plugins/check-tcp
---

check-tcp はTCPによるサーバーとの接続監視を行うプラグインです。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション             | 省略形 | 説明                                                                                                                                     | 初期値   |
| ---------------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| --service              |        | サービス名を指定（[対応サービス一覧](#対応サービス一覧)を参照）                                                                          |          |
| --hostname             | -H     | 疎通確認先のホスト名もしくはIPアドレス                                                                                                   |          |
| --port                 | -p     | 疎通確認先のポート番号                                                                                                                   |          |
| --send                 | -s     | サーバーに送信する文字列                                                                                                                 |          |
| --expect-pattern       | -e     | サーバーからのレスポンスに期待する正規表現パターン                                                                                       |          |
| --quit                 | -q     | サーバーから切断する際に送信する文字列                                                                                                   |          |
| --ssl                  | -S     | TLS接続の要否                                                                                                                            | false    |
| --unix-sock            | -U     | unixソケット                                                                                                                             |          |
| --no-check-certificate |        | 証明書検証の実施要否                                                                                                                     | false    |
| --timeout              | -t     | 接続タイムアウト時間（秒）                                                                                                               | 10       |
| --maxbytes             | -m     | 指定バイト数以上のデータを受信した場合にコネクションを切断                                                                               |          |
| --delay                | -d     | データ送信してから応答をポーリングするまでの待ち時間（秒）                                                                               |          |
| --warning              | -w     | Warningとする応答時間（秒）                                                                                                              |          |
| --critical             | -c     | Criticalとする応答時間（秒）                                                                                                             |          |
| --escape               | -E     | `--quit`で指定した文字列に`\n` `\r` `\t` `\\` を含む場合にエスケープを行う（未指定の場合は`--quit`で指定した文字列の末尾に`\r\n`を追加） | false    |
| --error-warning        | -W     | プラグインが異常終了した際のアラートステータスをWARNINGに設定する                                                                        | CRITICAL |

<h2 id="services">対応サービス一覧</h2>

`--service`に対応したサービス名を指定すると、各サービス名に対応したオプションによるチェックをします。

| サービス名 | --port | --send      | --expect-pattern         | --quit        | --ssl |
| ---------- | -----: | ----------- | ------------------------ | ------------- | ----- |
| FTP        | 21     |             | `^220`                   | `QUIT`        |       |
| POP        | 110    |             | `^\+OK`                  | `QUIT`        |       |
| SPOP       | 995    |             | `^\+OK`                  | `QUIT`        | ◯     |
| IMAP       | 143    |             | `^\* OK`                 | `a1 LOGOUT`   |       |
| SIMAP      | 993    |             | `^\* OK`                 | `a1 LOGOUT`   | ◯     |
| SMTP       | 25     |             | `^220`                   | `QUIT`        |       |
| SSMTP      | 465    |             | `^220`                   | `QUIT`        | ◯     |
| GEARMAN    | 7003   | version\n   | `\A[0-9]+\.[0-9]+\n\z`   |               |       |

<h2 id="config">エージェントへの設定例</h2>

`localhost`の`4224`番ポートの接続をチェックし、接続に3秒以上かかる場合はWarning、5秒以上かかる場合はCriticalとする設定は以下のようになります。

```
[plugin.checks.check-tcp-sample]
command = ["check-tcp", "-H", "localhost", "-p", "4224", "-w", "3", "-c", "5"]
```

上記設定をターミナルなどから直接実行して確認するには、以下のように実行してください。

```
check-tcp -H localhost -p 4224 -w 3 -c 5
```

IPv6アドレスを指定する場合は`[::1]`のように半角括弧`[]`で囲む形式で指定します。

```
[plugin.checks.check-tcp-v6-sample]
command = ["check-tcp", "-H", "[::1]", "-p", "4224", "-w", "3", "-c", "5"]
```

<h2 id="tips">Tips</h2>

check-tcpプラグインは接続チェックの際に、簡易的なメッセージのやりとりの正しさをチェックできます。

`--service` オプションを指定すると、対応しているプロトコルに応じたメッセージのチェックを併せて行いますが、`--send`や`--expect-pattern`を合わせることで独自プロトコルの監視にも代用できます。

例えばHTTPレスポンスのステータスラインのリーズンフレーズが想定したメッセージであることをチェックするには、以下のような設定ができます。

```
[plugin.checks.tcp_http404]
command = ["check-tcp", "-H", "localhost", "-p", "80", "-s", "GET / HTTP/1.1\\r\\n\\r\\n", "-e", "HTTP/1.1 404 Bad Request", "-E"]
```

事前に動作確認する場合は、以下のように実行します。

```
check-tcp -H localhost -p 80 -s "GET / HTTP/1.1\r\n\r\n" -e "HTTP/1.1 404 Bad Request" -E
```
<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-tcp
