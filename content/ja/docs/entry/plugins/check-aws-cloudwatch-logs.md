---
Title: チェックプラグイン - check-aws-cloudwatch-logs
Date: 2023-01-18T17:57:11+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-aws-cloudwatch-logs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889955367136
---

check-aws-cloudwatch-logs は CloudWatch Logs のロググループの監視を行うプラグインです。CloudWatch Logs のフィルターとパターンの構文で記述した条件に一致するログが検出された場合にアラートが発生します。このプラグインを実行するには CloudWatch Logs の FilterLogEvents API を利用するための設定が必要です。詳細は [認証と必要なポリシーについて](#policy) を参照してください。

[:contents]

<h2 id="specification">監視仕様</h2>

check-aws-cloudwatch-logs は初回実行時、現在時刻から 1 分前までのログを監視対象にします。

- 例: 12:00 に初回実行する場合
  - 11:59（startTime） 〜 12:00（endTime） が対象

また、実行の度に endTime の時刻を [State ファイル](#state-file) に記録し、2 回目以降は現在時刻からその時刻までのログを監視対象にします。

- 例: 12:00 に初回実行され、12:05 に 2 回目を実行する場合
  - 12:00（startTime） 〜 12:05（endTime） が対象

なお、前回の実行から 1 時間以上経過している場合は、初回実行と同様に現在時刻から 1 分前までのログが監視対象になります。

<h2 id="options">指定可能なオプション</h2>

| オプション            | 省略形 | 説明                                                                 | デフォルト値  |
| --------------------- | ------ | -------------------------------------------------------------------- | -------- |
| --log-group-name |  | 監視対象のロググループを指定 |  |
| --log-stream-name-prefix |  | ストリーム名をプレフィックスで絞り込み |  |
| --pattern | -p | 監視を行うための検索文字列を [CloudWatch Logs のフィルターとパターンの構文](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html) で記述 |  |
| --warning-over | -w | 検出パターンにマッチする行が指定値を超えたら Warning アラートを発生 | 0 |
| --critical-over | -c | 検出パターンにマッチする行が指定値を超えたら Critical アラートを発生 | 0 |
| --state-dir | -s | State ファイルの保存先ディレクトリパスを指定 | [State ファイルについて](#state-file) を参照 |
| --return | -r | パターンにマッチしたログ行をアラートに通知する（最大 1024 文字まで） |  |
| --max-retries | -t | CloudWatch Logs への最大問い合わせ試行回数の指定 | 3 |
| --help | -h | ヘルプを表示 |  |

<h2 id="config">エージェントへの設定例</h2>

CloudWatch Logs のロググループ `/aws/lambda/sample_log_group` に Error が出力されたことを検知する設定は以下のようになります。

```toml
[plugin.checks.aws-cloudwatch-logs-sample]
command = ["check-aws-cloudwatch-logs", "--log-group-name", "/aws/lambda/sample_log_group", "--pattern", "Error"]
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="pattern">--pattern の記述方法</h3>

CloudWatch Logs のフィルターとパターンの構文で記述します。記述方法は AWS のドキュメントを参照してください。

[https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html:embed:cite]

下記は主な条件指定の記述例です。

#### 単一の文字列の指定

下記は「ERROR」を含むログに一致します。

```
"--pattern", "ERROR"
```

#### AND 条件の指定

文字列をスペース区切りで記述します。下記は「ERROR」と「ARGUMENTS」を含むログに一致します。

```
"--pattern", "ERROR ARGUMENTS"
```

#### OR 条件の指定

文字列の先頭に `?` を記述します。下記は「ERROR」もしくは「ARGUMENTS」を含むログに一致します。

```
"--pattern", "?ERROR ?ARGUMENTS"
```

#### 除外する文字列の指定

除外する文字列の先頭に `-` を記述します。下記は「ERROR」を含み「ARGUMENTS」を含まないログに一致します。

```
"--pattern", "ERROR -ARGUMENTS"
```

除外する文字列だけを記述する場合は先頭にスペースが必要です。下記は「ARGUMENTS」を含まないログに一致します。

```
"--pattern", " -ARGUMENTS"
```

#### 正規表現の指定

正規表現のパターンを `%` で囲みます。サポートされている正規表現は CloudWatch Logs のフィルターパターンの仕様に依存します。

```
"--pattern", "%正規表現%"
```

#### JSON ログの条件指定

`{ $.キー = \"値\" }` の形で記述します。下記はキーが「level」、値が「ERROR」の JSON ログに一致します。

```
"--pattern", "{ $.level = \"ERROR\" }"
```

--pattern の指定を `'`（シングルクォーテーション）で囲む場合、エスケープ処理（`\`）は不要です。

```
"--pattern", '{ $.level = "ERROR" }'
```

#### 記号やスペースを含む文字列の指定

記号やスペースを含む文字列を `\"` で囲みます。--pattern の指定を `'`（シングルクォーテーション）で囲む場合、エスケープ処理（`\`）は不要です。

下記は「level: ERROR」を含むログに一致します。

```
"--pattern", "\"level: ERROR\""
```

```
"--pattern", '"level: ERROR"'
```

下記は「level: ERROR」を含まないログに一致します。

```
"--pattern", " -\"level: ERROR\""
```

```
"--pattern", ' -"level: ERROR"'
```

<h3 id="region">リージョンの指定方法</h3>

リージョンはオプションではなく以下のように環境変数で指定します。AWS_PROFILE 環境変数による名前付きプロファイルなどにも対応しています。

```
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="state-file">State ファイルについて</h3>

`--state-dir` オプションを指定しない場合、State ファイルは以下のディレクトリに `{監視対象ロググループ名}-<hash文字列>.json` の形式で保存されます。

- mackerel-agent 経由で実行した場合
  - `/var/tmp/mackerel-agent/check-cloudwatch-logs`
- 手動実行した場合
  - `/tmp/check-cloudwatch-logs`

<h2 id="policy">認証と必要なポリシーについて</h2>

check-aws-cloudwatch-logs は CloudWatch Logs の FilterLogEvents API を利用します。監視対象のロググループに対して以下のアクションを実行可能な IAM ユーザー / ロールの認証情報が利用できるようにしてください。

- `logs:FilterLogEvents`

認証情報の設定は以下の方法に対応しています。

- インスタンスプロファイルを使用する（EC2 のインスタンスから監視する場合）
- AWS_PROFILE 環境変数による名前付きプロファイルを使用する
- プラグイン設定の `env = {}` で `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` 環境変数を直接記述する

<h2 id="troubleshoot">トラブルシューティング</h2>

### Unknown: command timed out が発生する

流量の多いログストリームやストリーム数の多いロググループでは、プラグインの実行がタイムアウトすることがあります。タイムアウトはプラグインの設定に `timeout_seconds` を指定することで伸ばせます。ただし、`check_interval` を超えないように指定する必要があります。詳しくは [チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks) を参照してください。

なお、ログの量が多い場合にタイムアウトになるのは構造上避けられない問題です。対策としてはログの量を減らしたり、サンプリングするなどがあります。それが難しい場合は、流量の多いロググループの監視にも対応する [check-aws-cloudwatch-logs-insights](https://mackerel.io/ja/docs/entry/plugins/check-aws-cloudwatch-logs-insights) の利用を検討してください。


<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-cloudwatch-logs
