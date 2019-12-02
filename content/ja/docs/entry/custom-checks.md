---
Title: チェック監視項目を追加する
Date: 2015-05-14T12:14:36+09:00
URL: https://mackerel.io/ja/docs/entry/custom-checks
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450093836347
---

チェック監視は、Nagiosと同様にチェックプラグインの実行結果を監視する機能です。
エージェントはチェックプラグインを定期的に実行し、結果をMackerelに送信します。

ホストメトリック監視やサービスメトリック監視などの、Mackerel に送信されたメトリック値に対する閾値監視をおこなう**メトリック監視**とは、以下のような点で異なっています。

- メトリック監視では、ホスト側はメトリック値を Mackerel に投稿し、その値と閾値との比較・判定は Mackerel 側でおこなわれます
    - メトリック監視の場合、メトリックがグラフとして描画され、メトリックに対する監視ルールの設定はWebコンソールもしくは Web API からおこなうことができます
- チェック監視は、プラグインを用いてホスト内で OK / NG (`CRITICAL` or `WARNING` or `UNKNOWN`) の判定をおこない、その判定結果を Mackerel に対して投稿します
    - チェック監視の場合、メトリックが投稿されていないためグラフが描画されません。監視ルールの設定も、Webコンソールからはおこなうことはできず、ホストにインストールされた mackerel-agent に対して設定を追加することでおこないます

[f:id:mackerelio:20191126110514p:plain]

チェック監視を mackerel-agent で利用するためには、目的の監視処理をおこない、その結果に応じた終了ステータスを返却するプログラムが必要です。そのために利用できるものとして、公式のチェックプラグイン集を用意しています。詳しくは、[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins)をご覧ください。

また、チェック監視項目は1つあたり1ホストメトリックとしてカウントされます。プランごとの上限は[こちら](https://mackerel.io/ja/pricing) を、ホストあたりのメトリック上限やその超過時の仕様については[こちら](https://mackerel.io/ja/docs/entry/faq/contracts/limit-exceeded-conversion)をご覧ください。

<h2 id="setting">設定</h2>

[エージェントの設定ファイル](https://mackerel.io/ja/docs/entry/spec/agent#config-file)に、以下のような項目を追加します（例）:

```config
[plugin.checks.ssh]
command = ["ruby", "/path/to/check-ssh.rb"]
custom_identifier = "SOME_IDENTIFIER" # optional
notification_interval = 60
max_check_attempts = 1
check_interval = 5
timeout_seconds = 45
prevent_alert_auto_close = true
env = { HOST = "hostname", PORT = "port" }
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && ruby /path/to/notify_something.rb", env = { NOTIFY_API_KEY = "API_KEY" }, user = "someone", timeout_seconds = 45 }
memo = "This check monitor is ..."
```

- 項目名: 設定ファイル用のキーで、"plugin.checks." で始まっている必要があり、含まれるドットの数はちょうど2である必要があります。2つめのドット以降は監視ルールの名前として利用されます。
- command: エージェントが定期的に実行し、その終了ステータス/標準出力を監視結果として使用するコマンドです。コマンドは後述する仕様に沿って動作する必要があります。
- custom_identifier: 監視の実行結果を、 agent が動作しているホストではなく指定した識別子のホストの監視として送信します。
    - チェック結果が OK でなかった場合、ここで指定したホストのアラートとして発報されます。
    - AWS / Azure インテグレーションの連携ホストに監視を追加する場合などに有用です。詳細は[AWSインテグレーションのドキュメント](https://mackerel.io/ja/docs/entry/integrations/aws#plugin-custom-identifier)をご覧下さい。
- notification_interval: アラートの再送間隔を分で指定します。省略した場合、アラートは再送通知されません。10分未満は指定できません。10分未満を指定した場合は、10分間隔で通知を再送します。
- max_check_attempts: 最大試行回数を指定します。ここで指定した回数以上、OK以外のチェック結果が続いた場合にアラートを発報します。たとえば3が設定されている場合、OK以外の状態が3回続いた場合にアラートとなります。**`prevent_alert_auto_close` と併用した場合、指定した数値に関わらず `1` として扱われます。**
- check_interval: チェック監視の実行間隔を分で指定します。デフォルト値は1分です。設定可能な範囲は1分から60分で、1分未満の場合は1分、60分以上を指定した場合は60分間隔で監視が実行されます。
- timeout_seconds: プラグインの処理におけるタイムアウト時間を秒で指定します。デフォルト値は30秒です。各プラグイン実行の多重起動の制御はされませんので、プラグインの実行間隔を超えないような設定をすることを推奨します。
- prevent_alert_auto_close: このチェックプラグインによる監視でオープンされたアラートが自動クローズされなくなります。**`max_check_attempts` と併用した場合、 `max_check_attempts` は常に `1` として扱われます。**
- env: commandに渡す環境変数を指定できます。TOMLの[Table][]もしくは[Inline Table][]で指定します。
- action.command: `command` に設定したコマンド実行後に毎回実行されるアクションです。コマンドの実行結果に応じて行いたい処理がある場合に使用します。前回/今回のコマンドの結果などが環境変数として渡されます。実行結果は無視されます。
- action.env: action.commandに渡す環境変数を指定できます。TOMLの[Table][]もしくは[Inline Table][]で指定します。
- action.user: ここで指定したユーザーとして `action.command` を実行します。
- action.timeout_seconds : `action.command` の処理におけるタイムアウト時間を秒で指定します。デフォルト値は30秒です。
- memo: チェック監視に対してメモを設定できます。ここで指定した文字列は、アラート通知・アラート詳細画面・ホスト詳細ページで確認できます。

<h2 id="plugin">チェックプラグイン仕様</h2>

[Nagios][] のプラグインや [Sensu][] のチェックスクリプトなどとほぼ同じ仕様となっています。
設定ファイルで指定するコマンドの終了ステータスは以下のように扱われます。

|終了ステータス|意味|
|:-----------|------------:|:------------:|
| 0 | OK |
| 1 | WARNING |
| 2 | CRITICAL |
| 0,1,2以外 | UNKNOWN |

また、標準出力には補助的なメッセージを追加できます。メッセージ長の上限は1024文字までです。その出力はMackerelに送信され、ホスト詳細画面やアラート画面に可視化されるようになります。そのため、ご利用の際にはパスワードなどの秘匿情報が意図せず送信されないようにご注意下さい。

公式プラグインで利用しているユーティリティライブラリである、[github.com/mackerelio/checkers](https://github.com/mackerelio/checkers) を利用したプラグインの開発方法については、[checkersを利用してチェックプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/checkers) をご覧ください。

<h2 id="notification">チェック監視のアラート通知</h2>

アラートが発生した場合と、発生後にその状態が変化した場合に通知が行われます。「アラートの状態が変化した場合」とは具体的には下記の状態です。

- ステータスが変更になった場合
    - ex. CRITICAL -> WARNING, WARNING -> CRITICAL, CRITICAL -> OK
    - ステータスがOKになった場合も含みます

アラートの状態が変更した場合やメッセージ内容が変わった場合、その内容はアラート詳細画面上にも反映されます。メッセージ内容が変わっただけでは、通知は行われません。

<h2 id="action-env">アクションで利用可能な環境変数</h2>

|環境変数|説明|
|:-----------|:------------|
| MACKEREL_STATUS | 直前のコマンドの実行結果(`max_check_attempts` は考慮されません)。`OK`, `WARNING`, `CRITICAL`, `UNKNOWN` のいずれかです。 |
| MACKEREL_PREVIOUS_STATUS | 直前のコマンドの一つ前のコマンドの実行結果(`max_check_attempts` は考慮されません)。エージェント起動後初回は空文字列です。空文字列, `OK`, `WARNING`, `CRITICAL`, `UNKNOWN` のいずれかです。|
| MACKEREL_CHECK_MESSAGE | 直前のコマンドの実行結果メッセージ（ `command` の標準出力）。 |

<h2 id="example-ruby">Ruby によるプラグインのサンプル</h2>

6面ダイスの値をメッセージとして、値が4及び5のときは WARNINGを、6のときは CRITICAL を Mackerel に投稿するプラグインです。

```ruby
#!/usr/bin/env ruby
dice = rand(6)+1
puts "value is #{dice}"
exit (dice >= 6 ? 2 : dice >= 4 ? 1 : 0)
```

チェックプラグインを設定したエージェントを実行すると、以下のようにホスト詳細に監視中であることが表示されます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/S/Songmu/20150514/20150514114502.png)

アラートが発生した際には以下の様な表示となり、アラート詳細画面で詳細が確認できます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/S/Songmu/20150514/20150514115053.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/S/Songmu/20150514/20150514115052.png)

[Nagios]: http://www.nagios.org/
[Sensu]: https://sensuapp.org/
[Table]: https://github.com/toml-lang/toml#table
[Inline Table]: https://github.com/toml-lang/toml#inline-table
