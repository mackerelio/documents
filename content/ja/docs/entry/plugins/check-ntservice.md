---
Title: チェックプラグイン - check-ntservice
Date: 2022-12-15T16:23:57+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-ntservice
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889945319240
---

check-ntservice はWindowsサービスが起動しているかを監視するプラグインです。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション        | 省略形 | 必須 | 説明                                               |
| ----------------- | ------ | ---- | -------------------------------------------------- |
| --service-name    | -s     | ◯    | 指定した文字列を含むサービスを監視します           |
| --exclude-service | -x     |      | 指定した文字列を含む場合は監視の対象から除外します |
| --list-service    | -l     |      | サービス名の一覧を表示します                       |
| --exact           |        |      | --service-nameに完全一致するサービスを監視します |
| --status-as           |        |      | 監視ステータスを上書きします。たとえば `CRITICAL=WARNING` と記述した場合、監視ステータスが CRITICAL のときは WARNING になります。カンマ区切りで複数指定が可能です |

- オプションの複数指定、正規表現による指定には対応していません。
- `--exact`は mackerel-agent v0.78.0 以降に同梱されているプラグインで有効です。
- `--status-as`は mackerel-agent v0.85.0 以降に同梱されているプラグインで有効です。

<h2 id="config">エージェントへの設定例</h2>

W3SVCサービスの稼働状況を監視するには、以下のように設定します。

```toml
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "W3SVC"]
```

上記の設定を直接確認するには、コマンドプロンプトにて以下のように実行して確認できます。（プラグインにパスが通っている必要があります。）

```
check-ntservice --service-name W3SVC
```

<h2 id="tips">Tips</h2>

- `--service-name` オプションに指定するサービス名が不明な場合、`--list-service` オプションで指定可能なサービス名を事前に確認できます。

```
check-ntservice --list-service
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### 意図しないサービスの停止が検知されてしまう

check-ntserviceは `--service-name` オプションに指定した文字列を含むサービスのチェックを行うため、意図しないサービスの停止を検知してしまう場合があります。

その場合は `--exact` や `--exclude-service` オプションを併用し、対象サービスの特定や不要な文字列の除外をするようにしてください。

例えば`foo`と`foobar`というサービスが稼働していて`foo`の監視を行いたい場合は、`--exact`オプションを指定することで簡単に監視対象にできます。

```toml
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "foo", "--exact"]
```

また`--exclude-service`オプションを指定して、次のように除外することもできます。

```toml
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "foo", "--exclude-service", "foobar"]
```

### サービスが見つからないとUNKNOWNのアラートが発報する

条件に指定したサービスが見つからない場合に、`service does not exist.`のメッセージを含んだUNKNOWNのアラートが発報します。

正しいサービス名が指定されているか、`--list-service`オプションを活用するなどして見直しをしてください。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-ntservice
