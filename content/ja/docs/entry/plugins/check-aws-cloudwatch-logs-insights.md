---
Title: チェックプラグイン - check-aws-cloudwatch-logs-insights
Date: 2023-01-18T17:56:49+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-aws-cloudwatch-logs-insights
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889955367034
---

check-aws-cloudwatch-logs-insights は CloudWatch Logs のログデータの監視を行うプラグインです。このプラグインを実行するには CloudWatch Logs Insights の API を利用するための設定が必要です。詳細は [認証と必要なポリシーについて](#policy) を参照してください。check-aws-cloudwatch-logs との使い分けについては [check-aws-cloudwatch-logs との違い](#difference) を参照してください。

[:contents]

<h2 id="installation">インストール方法</h2>

check-aws-cloudwatch-logs-insights は [公式チェックプラグイン集](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) に含まれないため別途インストールが必要です。

### mkr を利用する

mkr を利用する場合はあらかじめインストールが必要です。インストール方法は以下のヘルプをご覧ください。

[https://mackerel.io/ja/docs/entry/advanced/cli:embed:cite]

mkr がインストールされている環境で以下のコマンドを実行します。

```
sudo mkr plugin install check-aws-cloudwatch-logs-insights
```

### ダウンロードして使用する

プラグインの実行ファイルは [GitHub Releases](https://github.com/mackerelio/check-aws-cloudwatch-logs-insights/releases) にて公開しています。mkr のインストール機能を利用せずに使用することもできますので、お使いの OS / アーキテクチャにあったファイルをダウンロードしてください。

<h2 id="specification">監視仕様</h2>

check-aws-cloudwatch-logs-insights は初回実行時、現在時刻の 5 分前の時刻から、そのさらに 1 分前までのログを監視対象にします。

- 例: 12:00 に初回実行する場合
  - 11:54（startTime） 〜 11:55（endTime） が対象

また、実行の度に endTime の時刻を [State ファイル](#state-file) に記録し、2 回目以降はその時刻を startTime にして、現在時刻の 5 分前までのログを監視対象にします。

- 例: 12:00 に初回実行され、12:05 に 2 回目を実行する場合
  - 11:55（startTime） 〜 12:00（endTime） が対象

なお、前回の実行から 90 分以上経過している場合は、初回実行と同様に、現在時刻の 5 分前の時刻から、そのさらに 1 分前までのログが監視対象になります。

<h2 id="options">指定可能なオプション</h2>

| オプション            | 省略形 | 説明                                                                 | デフォルト値  |
| --------------------- | ------ | -------------------------------------------------------------------- | -------- |
| --log-group-name |  | 監視対象のロググループを指定 |  |
| --filter | -f | 監視を行いたいログを絞り込むための CloudWatch Logs Insights のクエリ構文を記述 |  |
| --warning-over | -w | 検出パターンにマッチする行が指定値を超えたら Warning アラートを発生 | 0 |
| --critical-over | -c | 検出パターンにマッチする行が指定値を超えたら Critical アラートを発生 | 0 |
| --state-dir | -s | State ファイルの保存先ディレクトリパスを指定 | [State ファイルについて](#state-file) を参照 |
| --return | -r | パターンにマッチしたログ行をアラートに通知する（最大 1024 文字まで） |  |
| --help | -h | ヘルプを表示 |  |

<h3 id="filter">--filter の記述方法</h3>

CloudWatch Logs Insights のクエリ構文で記述します。記述方法は AWS のドキュメントを参照してください。

[https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html:embed:cite]


<h3 id="state-file">State ファイルについて</h3>

`--state-dir` オプションを指定しない場合、State ファイルは以下のディレクトリに `<hash文字列>.json` の形式で保存されます。

- mackerel-agent 経由で実行した場合
  - `/var/tmp/mackerel-agent/check-aws-cloudwatch-logs-insights`
- 手動実行した場合
  - `/tmp/check-aws-cloudwatch-logs-insights`


<h2 id="config">エージェントへの設定例</h2>

`/aws/lambda/some-lambda-function` ログストリームに `ERROR` という文字列を含むログが 1 件以上見つかった場合に Warning、10件以上見つかった場合に Critical を検知する設定は以下のようになります。

```toml
[plugin.checks.aws-cloudwatch-logs-insights-sample]
command = ["check-aws-cloudwatch-logs-insights", "--log-group-name", "/aws/lambda/some-lambda-function", "--filter", "filter @message like /ERROR/", "--warning-over", "1", "--critical-over", "10"]
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="region">リージョンの指定方法</h3>

リージョンはオプションではなく以下のように環境変数で指定します。AWS_PROFILE 環境変数による名前付きプロファイルなどにも対応しています。

```
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="policy">認証と必要なポリシーについて</h3>

check-aws-cloudwatch-logs-insights は CloudWatch Logs Insights の API を利用します。監視対象のロググループに対して以下のアクションを実行可能な IAM ユーザー / ロールの認証情報が利用できるようにしてください。

- `logs:GetQueryResults`
- `logs:StartQuery`
- `logs:StopQuery`

認証情報の設定は以下の方法に対応しています。

- インスタンスプロファイルを使用する（EC2 のインスタンスから監視する場合）
- `AWS_PROFILE` 環境変数による名前付きプロファイルを使用する
- プラグイン設定の `env = {}` で `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` 環境変数を直接指定する


<h2 id="difference">check-aws-cloudwatch-logs との違い</h2>

CloudWatch Logs のログを監視する公式のチェックプラグインには [check-aws-cloudwatch-logs](https://mackerel.io/ja/docs/entry/plugins/check-aws-cloudwatch-logs) もありますが、以下の点で異なります。

### 流量の多いロググループでも監視が行える

check-aws-cloudwatch-logs では流量の多いロググループの場合にログの読み込みがタイムアウトし、正常に監視が行えないことがありました。この check-aws-cloudwatch-logs-insights は CloudWatch Logs Insights API を利用しており、流量の多いロググループであっても監視が行えます。


### ログの絞り込みに用いる構文が異なる

- check-aws-cloudwatch-logs
  - CloudWatch Logs の [フィルターとパターンの構文](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html) で記述します。
- check-aws-cloudwatch-logs-insights
  - CloudWatch Logs Insights の [クエリ構文](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html) で記述します。

### 5 分前までのログが監視対象になる

[監視仕様](#specification) に記載の通り、このプラグインは現在時刻の 5 分前までのログを対象に監視を行います。この制限を受けない check-aws-cloudwatch-logs と比べると監視のリアルタイム性は低くなります。

### CloudWatch Logs Insights API の料金が発生する

check-aws-cloudwatch-logs は CloudWatch Logs の FilterLogEvents API を利用しており、この API リクエスト自体では AWS 側の費用は発生しません。check-aws-cloudwatch-logs-insights が利用している CloudWatch Logs Insights API ではスキャンしたログのデータ量に対して費用が発生します。CloudWatch Logs Insights API の料金については AWS の料金ページを参照してください。

[https://aws.amazon.com/jp/cloudwatch/pricing/:embed:cite]

<h2 id="troubleshoot">トラブルシューティング</h2>

### UNKNOWN: context canceledが発生する

- 考えられる原因
  - このエラーはプラグインの実行が中断された場合に発生します。本プラグインは AWS が提供する CloudWatch Logs Insights の API を利用して CloudWatch 上のログデータを取得しますが、API リクエストの送信や、応答が返って来るまでに時間がかかり、プラグインの実行がタイムアウトした可能性が考えられます（AWS が提供する API を利用するという性質上、詳細な原因についてはわかりかねます）。
- 影響
  - エラーが発生した際に監視対象となっていた期間のログのチェックに失敗します。監視対象となる期間については [監視仕様](#specification) を確認してください。
- 対処方法
  - 先述したタイムアウトが実際の原因である場合は、timeout_seconds でプラグイン実行のタイムアウトまでの時間を伸ばすことで、エラーの発生を回避できる可能性があります。timeout_seconds についてはチェック監視の [設定項目](https://mackerel.io/ja/docs/entry/custom-checks#items) を確認してください。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/check-aws-cloudwatch-logs-insights
