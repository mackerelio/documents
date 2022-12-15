---
Title: チェックプラグイン - check-ntservice
Date: 2022-12-15T16:23:57+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-ntservice
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889945319240
CustomPath: plugins/check-ntservice
---

check-ntservice はWindowsサービスが起動しているかを監視するプラグインです。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション        | 省略形 | 必須 | 説明                                               |
| ----------------- | ------ | ---- | -------------------------------------------------- |
| --service-name    | -s     | ◯    | 指定した文字列を含むサービスを監視します           |
| --exclude-service | -E     |      | 指定した文字列を含む場合は監視の対象から除外します |
| --list-service    | -l     |      | サービス名の一覧を表示します                       |

- オプションの複数指定、正規表現による指定には対応していません。

<h2 id="config">エージェントへの設定例</h2>

W3SVCサービスの稼働状況を監視するには、以下のように設定します。

```
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

その場合は `--exclude-service` オプションを併用し、対象としないサービスを除外するようにしてください。

例えば`foo`というサービスの監視で`foobar`という別サービスを意図せず検知してしまう場合は以下のように設定します。

```
[plugin.checks.ntsvc_w3svc]
command = ["check-ntservice", "--service-name", "foo", "--exclude-service", "foobar"]
```

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-ntservice
