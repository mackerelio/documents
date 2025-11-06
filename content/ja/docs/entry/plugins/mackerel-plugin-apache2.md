---
Title: メトリックプラグイン - mackerel-plugin-apache2
Date: 2022-12-15T16:26:12+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-apache2
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889945319738
---

mackerel-plugin-apache2 は Apache2 に関する統計情報をメトリックとして監視するためのプラグインです。

このプラグインの使用には、ApacheのStatusモジュール（mod_status）が有効で且つプラグインからのアクセスができる必要があります。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

mackerel-plugin-apache2 で監視できるメトリックは以下となります。

### Apache Workers

稼働しているサーバープロセス/スレッド数を監視できます。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                            |
| ---------------- | ----------------------------------- | ---- | ------------ | ----------------------------------------------- |
| Busy Workers     | custom.apache2.workers.busy_workers | -    | ◯            | リクエスト処理中のサーバープロセス/スレッド数   |
| Idle Workers     | custom.apache2.workers.idle_workers | -    | ◯            | リクエスト待ち状態のサーバープロセス/スレッド数 |

### Apache Bytes

httpdプロセスから送出されたデータ量を監視できます。

| メトリック表示名 | メトリック名                    | 差分 | 積み上げ表示 | 説明                      |
| ---------------- | ------------------------------- | ---- | ------------ | ------------------------- |
| Bytes Sent       | custom.apache2.bytes.bytes_sent | ◯    | -            | 1分間の合計通信量（byte） |

### Apache CPU Load

httpdプロセスのCPU使用率を監視できます。

| メトリック表示名 | メトリック名                | 差分 | 積み上げ表示 | 説明      |
| ---------------- | --------------------------- | ---- | ------------ | --------- |
| CPU Load         | custom.apache2.cpu.cpu_load | -    | -            | CPU使用率 |

### Apache Requests

リクエスト数を監視できます。

| メトリック表示名 | メトリック名                | 差分 | 積み上げ表示 | 説明                      |
| ---------------- | --------------------------- | ---- | ------------ | ------------------------- |
| Requests         | custom.apache2.req.requests | ◯    | -            | 1分間のリクエスト受信回数 |

### Apache Scoreboard

スコアボード（稼働中のサーバープロセスの状態）の各メトリックを監視できます。

| メトリック表示名       | メトリック名                      | 差分 | 積み上げ表示 | 説明                                             |
| ---------------------- | --------------------------------- | ---- | ------------ | ------------------------------------------------ |
| Waiting for connection | custom.apache2.scoreboard.score-_ | -    | ◯            | プロセス/スレッドを起動し、アクセス待ちの件数    |
| Starting up            | custom.apache2.scoreboard.score-S | -    | ◯            | プロセス/スレッドが起動中の件数                  |
| Reading request        | custom.apache2.scoreboard.score-R | -    | ◯            | クライアントからのリクエストを読み込んでいる件数 |
| Sending reply          | custom.apache2.scoreboard.score-W | -    | ◯            | クライアントにレスポンスしている件数             |
| Keepalive              | custom.apache2.scoreboard.score-K | -    | ◯            | KeepAliveによりリクエストを待機している件数      |
| DNS lookup             | custom.apache2.scoreboard.score-D | -    | ◯            | 名前解決をしている件数                           |
| Closing connection     | custom.apache2.scoreboard.score-C | -    | ◯            | 接続を終了している件数                           |
| Logging                | custom.apache2.scoreboard.score-L | -    | ◯            | ログを出力している件数                           |
| Gracefully finishing   | custom.apache2.scoreboard.score-G | -    | ◯            | Gracefulリスタートにおける処理終了待ちの件数     |
| Idle cleanup           | custom.apache2.scoreboard.score-I | -    | ◯            | プロセス/スレッドが停止している件数              |
| Open slot              | custom.apache2.scoreboard.score-  | -    | ◯            | スロットが空いている件数                         |


<h2 id="options">指定可能なオプション</h2>

プラグインに指定可能なオプションは以下の通りです。

| オプション            | 省略形 | 環境変数                | 説明                                     | 初期値              |
| --------------------- | ------ | ----------------------- | ---------------------------------------- | ------------------- |
| --http_host           | -o     |                         | httpdがLISTENしているIPアドレス          | 127.0.0.1           |
| --http_port           | -p     |                         | httpdがLISTENしてるポート番号            | 80                  |
| --header              | -H     |                         | リクエスト時に付与するHTTPヘッダー       |                     |
| --status_page         | -s     |                         | 統計情報を提供するURLパス                | /server-status?auto |
| --tempfile            | -t     | MACKEREL_PLUGIN_WORKDIR | 一時ファイルの保存先パス                 |                     |
| --metric-key-prefix   |        |                         | メトリック名にprefixとして付与する文字列 | apache2             |
| --metric-label-prefix |        |                         | グラフ名にprefixとして付与する文字列     | Apache              |

- 一時ファイル（tempfile）はデフォルトでは`/var/tmp/mackerel-agent`もしくは`/tmp`配下に`mackerel-plugin-{Prefixなどの文字列}-{ハッシュ文字列}`の形式で作成されます

<h2 id="config">エージェントへの設定例</h2>

```toml
[plugin.metrics.apache2]
command = ["mackerel-plugin-apache2", "-p", "1080"]
```

上記の設定を直接確認するには、ターミナルから以下のように実行して確認できます。

```
$ mackerel-plugin-apache2 -p 1080
```

<h2 id="config-apache">Apacheの設定例</h2>

mackerel-plugin-apache2を使用するために、ApacheのStatusモジュール（mod_status）が有効であることを確認してください。

以下は `http://127.0.0.1:1080/server-status` での公開を想定した設定です。

```
ExtendedStatus On
<VirtualHost 127.0.0.1:1080>
  <Location /server-status>
    SetHandler server-status
    Order deny,allow
    Deny from all
    Allow from localhost
  </Location>
</VirtualHost>
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### メトリックが取得できない

プラグインを設定してもメトリックが取得できない場合、設定の未反映やApache側でのStatusモジュールの設定の誤りなどが原因であることが考えられます。以下のような点をご確認ください。

- プラグインの設定後に mackerel-agent の再起動を実施しているか
  - mackerel-agent の再起動を行わないと設定が反映されないため、設定変更後は再起動を実施してください
- Apache側でStatusモジュールの設定後、curlコマンドによる疎通確認が正常であるか
  - 前述の設定例の通りに設定した場合、以下のコマンドで 200 OK のレスポンスが受信できることをご確認ください

    ```
    curl http://127.0.0.1:1080/server-status
    ```

  - `http`から`https`へのリダイレクトをmod_rewriteで設定している場合、プラグインはリダイレクトの追跡を行いません。Statusモジュールのエンドポイントはリダイレクトの対象から外してください
  - curlコマンドでStatusモジュールの結果が取得できない場合、設定が誤っているかApacheの再起動漏れにより未反映となっている場合があるのでご確認ください

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-apache2

<h2 id="reference">参考</h2>

- [httpd/scoreboard.h at trunk · apache/httpd](https://github.com/apache/httpd/blob/trunk/include/scoreboard.h)

