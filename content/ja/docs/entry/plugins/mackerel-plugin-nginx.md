---
Title: メトリックプラグイン - mackerel-plugin-nginx
Date: 2023-08-30T16:25:17+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-nginx
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/820878482963047227
---

mackerel-plugin-nginxは、nginxのstub_statusを元にnginxの状態を可視化するプラグインです。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

#### Nginx Connections

| メトリック表示名 | メトリック名 | 差分 | 積み上げ表示 | 説明 |
| ------------------ | ------------------------------------ | - | - | ----------------------------------------- |
| Active connections | custom.nginx.connections.connections | - | - | Waiting 状態を含むクライアントからの現在の接続数 |


#### Nginx requests

| メトリック表示名 | メトリック名 | 差分 | 積み上げ表示 | 説明 |
| -------------------- | ------------------------------ | - | - | ---------------------------- |
| Accepted connections | custom.nginx.requests.accepts  | ◯ | - | 受け付けたクライアント接続の総数  |
| Handled connections  | custom.nginx.requests.handled  | ◯ | - | 処理したクライアント接続の総数    |
| Handled requests     | custom.nginx.requests.requests | ◯ | - | クライアントからのリクエストの総数 |

#### Nginx connection status

| メトリック表示名 | メトリック名 | 差分 | 積み上げ表示 | 説明 |
| ------- | -------------------------- | - | - | ----------------------------------------------------- |
| Reading | custom.nginx.queue.reading | - | - | リクエストヘッダを読んでいる現在の接続数                     |
| Writing | custom.nginx.queue.writing | - | - | クライアントにレスポンスを書き戻している現在の接続数           |
| Waiting | custom.nginx.queue.waiting | - | - | リクエストを待っているアイドルクライアントコネクションの現在の数 |

<h2 id="options">指定可能なオプション</h2>

プラグインに指定可能なオプションは以下の通りです。

| オプション | 環境変数 | 説明 | 初期値 |
| ---------- | - | ---------------------------------------------------------------------- | ------------ |
| -uri      |   | 接続先のURI                                                              |              |
| -scheme   |   | 接続する際のHTTPスキーム(http か https)                                    | http         |
| -host     |   | 接続先のホスト名                                                          | localhost     |
| -port     |   | 接続先のポート番号                                                         | 8080         |
| -path     |   | stub_status が提供されているパス                                           | /nginx_status |
| -tempfile |   | 一時ファイルの保存先パス                                                    |               |
| -header   |   | 接続時に付与する追加のヘッダー (2つ以上指定する場合はそれぞれに `-header` が必要) |               |


<h2 id="config">エージェントへの設定例</h2>

ローカルホスト上で、8081番ポートで `/stub_status` として stub_status を提供している nginx を監視するには次のように設定します。

```toml
[plugin.metrics.nginx]
command = ["/path/to/mackerel-plugin-nginx","--port","8081","--path","/stub_status"]
```

上記設定の場合、次のように実行して動作確認できます。

```
$ mackerel-plugin-nginx --port 8081 --path /stub_status
```

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-nginx

<h2 id="reference">参考</h2>

stub_statusを有効にする設定方法やプラグインが参照する各変数の詳細については、以下 ngx_http_stub_status_module の説明をご覧ください。

[Module ngx_http_stub_status_module](http://nginx.org/en/docs/http/ngx_http_stub_status_module.html)




