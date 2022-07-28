---
Title: チェック監視項目を追加する
Date: 2015-05-14T12:14:36+09:00
URL: https://mackerel.io/ja/docs/entry/custom-checks
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450093836347
---

チェック監視はチェックプラグインの実行結果を監視する機能です。エージェントはチェックプラグインを定期的に実行し、その結果を Mackerel に送信します。チェック監視項目は 1 つあたり 1 ホストメトリックとしてカウントされます。ホストメトリック監視やサービスメトリック監視との違いについては [メトリック監視とチェック監視の違い](https://mackerel.io/ja/docs/entry/custom-checks#difference) を参照してください。

[:contents]

<h2 id="check-plugin">チェックプラグインについて</h2>

チェック監視を行うためには目的の監視処理を行うプログラムが必要です。そのために利用できるものとして、公式のチェックプラグイン集を公開しています。詳しくは、[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) を参照してください。

また、チェックプラグインにはユーザが自作したプログラムも使用できます。そのプログラムは目的の監視処理を行い、監視結果に応じた終了ステータスを返す必要があります。詳しくは [チェックプラグイン仕様](https://mackerel.io/ja/docs/entry/custom-checks#specification) を参照してください。

<h2 id="setting">エージェントへの設定例</h2>

チェック監視の設定は [エージェントの設定ファイル](https://mackerel.io/ja/docs/entry/spec/agent#config-file) に追加します。以下は `check-ssh.rb` という自作のプログラムを使用した監視の設定例です。各項目の意味は [設定項目](https://mackerel.io/ja/docs/entry/custom-checks#items) を参照してください。（必須項目以外は必要に応じて設定します）

```config
[plugin.checks.ssh]
command = ["ruby", "/path/to/check-ssh.rb"]
check_interval = 5
timeout_seconds = 45
max_check_attempts = 3
prevent_alert_auto_close = true
notification_interval = 60
custom_identifier = "SOME_IDENTIFIER"
env = { HOST = "hostname", PORT = "port" }
memo = "This check monitor is ..."
```

<h3 id="items">設定項目</h3>

| 設定項目 | 必須 | 説明 | デフォルト値 |
|---|---|---|---|
| [plugin.checks.XXX] | ◯ | 設定ファイル用のキーです。`plugin.checks.` までは固定です。2 つ目のドット以降（XXX）の文字列が監視ルールの名前として使用されます。XXX の部分にドットを含めることはできません。 | なし |
| command | ◯ | エージェントが定期的に実行し、その終了ステータスや標準出力内容を監視結果として使用します。公式チェックプラグインのほか、ユーザが自作したプログラムや任意のコマンドなども実行できます。 | なし |
| check_interval |  | チェック監視の実行間隔を分で指定します。エージェントのバージョンが v0.67.0 以降であれば `10m` や `1h` のような表現でも記述できます。設定可能な範囲は 1 分から 60 分です。1 分未満を指定した場合は 1 分、60 分以上を指定した場合は 60 分として扱われます。 | 1（分） |
| timeout_seconds |  | プラグインの処理におけるタイムアウト時間を秒で指定します。各プラグイン実行の多重起動の制御はされませんので、プラグインの実行間隔（check_interval）を超えないように設定をすることを推奨します。 | 30（秒） |
| max_check_attempts |  | 最大試行回数を指定します。ここで指定した回数以上、OK 以外の結果が連続した場合にアラートが発生します。<br>**`prevent_alert_auto_close` と併用した場合、指定した数値に関わらず `1` として扱われます。** | 1 |
| prevent_alert_auto_close |  | このチェックプラグインによる監視で発生したアラートが自動クローズされなくなります。<br>**`max_check_attempts` と併用した場合、 `max_check_attempts` は常に `1` として扱われます。** | false |
| notification_interval |  | アラート通知の再送間隔を分で指定します。エージェントのバージョンが v0.67.0 以降であれば `10m` や `1h` のような表現でも記述できます。省略した場合は再送されません。10 分未満は指定できません。10 分未満を指定した場合は 10 分として扱われます。 | 指定なし |
| custom_identifier |  | 監視の実行結果を mackerel-agent が動作しているホストではなく、ここで指定した識別子のホストの監視として扱います。チェック結果が OK でなかった場合、ここで指定したホストのアラートとして発報されます。<br>AWS / Azure / Google Cloud インテグレーションで連携したホストなど、エージェントがインストールできないホストにチェック監視を追加する場合に有用です。詳細は [AWSインテグレーションのドキュメント](https://mackerel.io/ja/docs/entry/integrations/aws#plugin-custom-identifier) を参照してください。<br>この値は API の [ホスト情報の取得](https://mackerel.io/ja/api-docs/entry/hosts#get)  で確認が可能です。 | 指定なし |
| env |  | 環境変数を設定できます。設定を行ったプラグインでのみ有効です。 | 指定なし |
| action |  | `command` に設定したコマンドの実行直後に、ここに記述したアクションが毎回実行されます。詳しくは [action の記述方法](https://mackerel.io/ja/docs/entry/custom-checks#action-setting) を参照してください。 | 指定なし |
| memo |  | チェック監視に対してメモを設定できます。ここで指定した文字列は、アラート通知、アラート詳細画面、ホスト詳細画面で確認できます。 | 指定なし |

<h3 id="action-setting"> action の記述方法</h3>

`action = {}` の形で以下の項目を記述します。各項目はカンマ区切りで同時に記述が可能です。

| 設定値 | 説明 |
|---|---|
| command | `command` に設定したコマンドの実行直後に毎回実行されます。コマンドの実行結果に応じて行いたい処理がある場合などに使用します。後述の [環境変数](https://mackerel.io/ja/docs/entry/custom-checks#action-env) が利用できます。（プラグインの `command` と区別するために本項では `action.command` として扱います） |
| env | `action.command` に渡す環境変数を指定できます。TOMLの [Table][] もしくは [Inline Table][] で指定します。 |
| user | ここで指定したユーザーで `action.command` を実行します。Windows 環境には非対応です。 |
| timeout_seconds | `action.command` の処理におけるタイムアウト時間を秒で指定します。（デフォルト: 30秒） |

以下は `command` の実行結果（MACKEREL_STATUS）が OK 以外の場合に `action.command` を実行する設定の例です。

```
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && ruby /path/to/notify_something.rb", env = { NOTIFY_API_KEY = "API_KEY" }, user = "someone", timeout_seconds = 45 }
```

<h3 id="action-env">action で利用可能な環境変数</h3>

|環境変数|説明|
|:-----------|:------------|
| MACKEREL_STATUS | 直前の `command` の実行結果です。`OK`, `WARNING`, `CRITICAL`, `UNKNOWN` のいずれかになります。（`max_check_attempts` は考慮されません） |
| MACKEREL_PREVIOUS_STATUS | 前回の `command` の実行結果です。エージェント起動後の初回実行時は空文字列です。2 回目以降は `OK`, `WARNING`, `CRITICAL`, `UNKNOWN` のいずれかになります。（`max_check_attempts` は考慮されません） |
| MACKEREL_CHECK_MESSAGE | 直前の `command` の標準出力結果です。Windows では結果を取得できない場合があります。 |

<h2 id="specification">チェックプラグイン仕様</h2>

`command` の終了ステータスに応じて以下のように扱われます。

| 終了ステータス | アラートレベル |
|-----------|------------|
| 0 | OK |
| 1 | WARNING |
| 2 | CRITICAL |
| 0, 1, 2 以外 | UNKNOWN |

また、標準出力には補助的なメッセージを追加できます。メッセージ長の上限は 1024 文字までです。その出力は Mackerel に送信され、ホスト詳細画面やアラート詳細画面に表示されます。そのため、パスワードなどの秘匿情報が送信されないようにご注意ください。

公式プラグインで利用しているユーティリティライブラリである、[github.com/mackerelio/checkers](https://github.com/mackerelio/checkers) を利用したプラグインの開発方法については、[checkersを利用してチェックプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/checkers) を参照してください。

<h2 id="notification">チェック監視のアラート通知</h2>

アラートが発生した場合と、発生後にアラートのステータスが変化した場合に通知が行われます。ステータスが OK になった場合も含みます。

以下はアラートのステータス変化とその際の通知の有無を示した例です。

| 監視実行回数 | 1 回目 | 2 回目 | 3 回目 | 4 回目 | 5 回目 | 6 回目 |
|---|---|---|---|---|---|---|
| ステータス | CRITICAL | WARNING | WARNING | CRITICAL | CRITICAL | OK |
| アラート通知 | あり | あり | なし | あり | なし | あり |

アラートのステータスやメッセージの内容が変化した場合、その内容はアラート詳細画面にも反映されます。なお、メッセージの内容が変化しただけの場合は通知は行われません。

<h2 id="difference">メトリック監視とチェック監視の違い</h2>

ホストメトリック監視やサービスメトリック監視などのメトリック監視とチェック監視では以下のような点で異なります。

- メトリック監視
  - ホスト側はメトリック値を Mackerel に投稿します。その値と閾値との比較やアラート通知は Mackerel 側で行われます。
  - メトリックがグラフとして描画されます。
  - 監視ルールの設定は Web コンソールもしくは Web API で行います。
  - 監視ルールの追加による料金は発生しません。（課金対象はメトリックそのものです）
- チェック監視
  - プラグインを実行するホスト内で OK / NG (`CRITICAL` or `WARNING` or `UNKNOWN`) の判定を行い、その結果を Mackerel に送信します。Mackerel は受け取った結果に応じてアラートを通知します。
  - メトリックが投稿されないためグラフは描画されません。
  - 監視ルールの設定は mackerel-agent の設定ファイルで行います。Web コンソールで設定の追加や変更はできません。
  - 1 つあたり 1 ホストメトリックとしてカウントされます。1 ホストあたりのメトリック数の上限については [料金](https://mackerel.io/ja/pricing) を参照してください。また、上限超過時の仕様については [プラン上限超過時のホスト台数換算について](https://support.mackerel.io/hc/ja/articles/360040109431-%E3%83%97%E3%83%A9%E3%83%B3%E4%B8%8A%E9%99%90%E8%B6%85%E9%81%8E%E6%99%82%E3%81%AE%E3%83%9B%E3%82%B9%E3%83%88%E5%8F%B0%E6%95%B0%E6%8F%9B%E7%AE%97%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6) を参照してください。
- 監視イメージ
  - 【左】メトリック監視【右】チェック監視
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200108/20200108154244.png)


<h2 id="example-ruby">Ruby によるプラグインのサンプル</h2>

6 面ダイスの値をメッセージとして、値が 4 及び 5 のときは WARNING を、6 のときは CRITICAL を Mackerel に投稿するプラグインです。

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
