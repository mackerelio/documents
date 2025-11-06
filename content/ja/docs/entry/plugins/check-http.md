---
Title: チェックプラグイン - check-http
Date: 2022-11-21T18:10:13+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-http
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889938595393
---

check-http は任意のサーバーに対してHTTP接続による監視を行うプラグインです。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション             | 省略形 | 必須 | 説明                                                                                       | 複数指定 | 初期値       |
| ---------------------- | ------ | ---- | ------------------------------------------------------------------------------------------ | -------- | ------------ |
| --url                  | -u     | ◯    | 接続先URL                                                                                  |          |              |
| --status               | -s     |      | HTTPレスポンスコードごとの監視結果を`key=value`の形式で指定                                | ○        |              |
| --no-check-certificate |        |      | 証明書の検証を行わない                                                                     |          |              |
| --source-ip            | -i     |      | ソースIPアドレス                                                                           |          |              |
|                        | -H     |      | HTTPリクエストヘッダー                                                                     | ○        |              |
| --pattern              | -p     |      | HTTPレスポンスボディのチェックを行う正規表現                                               |          |              |
| --max-redirects        |        |      | リダイレクトをフォローする最大回数                                                         |          | 10           |
| --method               | -m     |      | HTTPリクエストメソッド（`GET`, `HEAD`, `POST`, `PUT`のいずれかを指定）                     |          | GET          |
| --connect-to           |        |      | HOST1:PORT1の代わりにHOST2:PORT2に接続を行う。`HOST1:PORT1:HOST2:PORT2`の形式で指定        | ○        |              |
| --proxy                | -x     |      | HTTPプロキシを経由してリクエストを行う。`[PROTOCOL://][USER:PASS@]HOST[:PORT]`の形式で指定 |          | `PORT`は1080 |
| --user                 |        |      | Basic認証のユーザーIDとパスワードを`USER[:PASSWORD]`の形式で指定                           |          |              |
| --require-bytes        | -B     |      | レスポンスサイズが指定したバイト数と一致することを確認                                     |          | -1           |
| --body                 | -d     |      | リクエストボディ                                                                           |          |              |
| --min-bytes            | -g     |      | レスポンスが少なくも指定したバイト数以上であることを確認                                   |          | -1           |
| --timeout              | -t     |      | タイムアウト時間（秒）                                                                     |          | 0            |
| --cert-file            |        |      | クライアント認証に使用する証明書を指定                                                     |          |              |
| --key-file             |        |      | クライアント認証に使用する鍵ファイルを指定                                                 |          |              |
| --ca-file              |        |      | クライアント認証に使用するCA証明書を指定                                                   |          |              |

<h3 id="options">HTTPレスポンスコードごとの監視結果</h3>

`--status`オプションでHTTPレスポンスコードごとの監視結果を指定できますが、未指定の場合の監視結果は以下の通りです。

| HTTPレスポンスコード | 監視結果 |
| -------------------- | -------- |
| 100番台              | OK       |
| 200番台              | OK       |
| 300番台              | OK       |
| 400番台              | WARNING  |
| それ以外             | CRITICAL |

- `--status`オプションには`key=value`の形式でレスポンスコードごとの監視結果を設定できます。
- HTTPレスポンスコードは`200-404`のように範囲で指定することも可能です。

<h2 id="config">エージェントへの設定例</h2>

`https://mackerel.io/`を監視するには以下のように設定します。

```toml
[plugin.checks.https-mackerelio]
command = ["check-http", "-u", "https://mackerel.io"]
```

HTTPレスポンスが`404`の監視結果を`OK`とする場合は以下のように設定します。

```toml
[plugin.checks.https-mackerelio]
command = ["check-http", "-u", "https://mackerel.io", "--status", "404=OK"]
```

HTTPステータスコードを`200-404`のように指定することも可能です。

```toml
[plugin.checks.https-mackerelio]
command = ["check-http", "-u", "https://mackerel.io", "--status", "200-404=OK"]
```

<h2 id="tips">Tips</h2>

通常、[設定例](##設定例)にあるようなインターネットに公開されているエンドポイントの監視は、[URL外形監視](https://mackerel.io/ja/docs/entry/external-monitoring)にてMackerelの本体サービスから監視することをお勧めしています。

check-http プラグインはインターネットから直接監視することのできない内部向けAPIなど同一ネットワーク内から監視する内形（内部）監視の手段としてご利用ください。

またURL外形監視では対応していないクライアント証明書が必要なURLの監視などの代用としてもご活用いただけます。

<h2 id="repository">リポジトリ</h2>
https://github.com/mackerelio/go-check-plugins/tree/master/check-http
