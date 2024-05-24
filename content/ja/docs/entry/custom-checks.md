---
Title: チェック監視項目を追加する
Date: 2015-05-14T12:14:36+09:00
URL: https://mackerel.io/ja/docs/entry/custom-checks
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450093836347
---

チェック監視はチェックプラグインの実行結果を監視する機能です。エージェントはチェックプラグインを定期的に実行し、その結果を Mackerel に送信します。チェック監視項目は 1 つあたり 1 ホストメトリックとしてカウントされます。ホストメトリック監視やサービスメトリック監視との違いについては [メトリック監視とチェック監視の違い](#difference) を参照してください。

[:contents]

<h2 id="check-plugin">チェックプラグインについて</h2>

チェック監視を実施するには目的の監視を行うプラグインが必要です。Mackerel では公式のチェックプラグイン集を公開しています。利用方法については [チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) を参照してください。

また、公式チェックプラグインに限らず、[チェックプラグイン仕様](#specification) に沿ってユーザー独自のチェック監視を実装することも可能です。後述の [チェックプラグインのサンプル](#example) にて、シェルスクリプト、PowerShell、bat ファイルで作成したチェックプラグインのサンプルを紹介しています。 

<h2 id="setting">エージェントへの設定例</h2>

チェック監視の設定は [エージェントの設定ファイル](https://mackerel.io/ja/docs/entry/spec/agent#config-file) に追加します。以下は [チェックプラグインのサンプル](#example) で紹介しているシェルスクリプトを使用した監視の設定例です。各項目の意味は [設定項目](#items) を参照してください。必須項目以外は必要に応じて設定してください。

```config
[plugin.checks.filecount]
command = ["/path/filecount.sh"]
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

エージェントへの設定例のように、監視ルールのキー `[plugin.checks.XXX]` に続けて記述します。各設定項目は監視ルールごとに設定が必要です。

| 設定項目 | 必須 | 説明 | デフォルト値 |
|---|---|---|---|
| [plugin.checks.XXX] | ◯ | 監視ルールのキーとなる値をドット（.）区切りの 3 階層で、設定ファイル内で一意に定義します。2 階層目の `plugin.checks.` までは固定です。3 階層目の `XXX` に記述した文字列が監視ルール名として使用されます。`XXX` にドット（.）を含めることはできません。 |  |
| command | ◯ | エージェントが定期的に実行し、その終了ステータスや標準出力内容を監視結果として使用します。公式チェックプラグインのほか、ユーザーが自作したプラグインや、任意のコマンドを実行できます。 |  |
| check_interval |  | チェック監視の実行間隔を分で指定します。エージェントのバージョンが v0.67.0 以降であれば `10m` や `1h` のような表現でも記述できます。設定可能な範囲は 1 分から 60 分です。1 分未満を指定した場合は 1 分、60 分以上を指定した場合は 60 分として扱われます。 | 1 分 |
| timeout_seconds |  | `command` で指定したプラグインの処理におけるタイムアウト時間を秒で指定します。`check_interval` を超えないように設定してください。 | 30 秒 |
| max_check_attempts |  | 最大試行回数を指定します。ここで指定した回数以上、OK 以外の結果が連続した場合にアラートが発生します。<br>**`prevent_alert_auto_close` と併用した場合、指定した数値に関わらず `1` として扱われます。** | 1 |
| prevent_alert_auto_close |  | 通常はアラート発生以後の監視結果が OK になるとそのアラートは自動で Closed になりますが、これが true の場合は Opend のままになります。<br>**`max_check_attempts` と併用した場合、 `max_check_attempts` は常に `1` として扱われます。** | false |
| notification_interval |  | アラート通知の再送間隔を分で指定します。エージェントのバージョンが v0.67.0 以降であれば `10m` や `1h` のような表現でも記述できます。10 分未満を指定した場合は 10 分として扱われます。設定しない場合、通知の再送は行いません。 | null |
| custom_identifier |  | 監視結果をエージェントが動作しているホストではなく `custom_identifier` で指定したホストに投稿します。監視によるアラートは指定したホストのアラートとして発報されます。`custom_identifier` はホスト詳細画面で確認できます。<br>AWS / Azure / Google Cloud インテグレーションで連携したホストなど、エージェントがインストールできないホストに対してチェック監視を実行する場合に有用です。詳細は [AWSインテグレーションのドキュメント](https://mackerel.io/ja/docs/entry/integrations/aws#plugin-custom-identifier) を参照してください。 | null |
| env |  | 環境変数を設定できます。これは設定を行った監視ルールの `command` でのみ有効です。 | null |
| action |  | `command` に設定したコマンドの実行直後に、ここに記述したアクションが毎回実行されます。詳しくは [action の記述方法](#action-setting) を参照してください。 | null |
| memo |  | チェック監視に対してメモを設定できます。ここで指定した文字列は、アラート通知、アラート詳細画面、ホスト詳細画面で確認できます。最大 250 文字まで。 | null |


<h3 id="action-setting"> action の記述方法</h3>

`action = {}` の形で以下の項目を記述します。各項目はカンマ区切りで同時に記述が可能です。

| 設定値 | 説明 | デフォルト値 |
|---|---|---|
| command | `command` に設定したコマンドの実行直後に毎回実行されます。コマンドの実行結果に応じて行いたい処理がある場合などに使用します。後述の [環境変数](#action-env) が利用できます。監視ルールの `command` と区別するために当ページでは `action.command` として扱います。 |  |
| env | `action.command` に渡す環境変数を指定できます。TOMLの [Table][] もしくは [Inline Table][] で指定します。 | null |
| user | ここで指定したユーザーで `action.command` を実行します。Windows 環境には非対応です。 | root |
| timeout_seconds | `action.command` の処理におけるタイムアウト時間を秒で指定します。 | 30 秒 |

action の記述例

```
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && ruby /path/to/notify_something.rb", env = { NOTIFY_API_KEY = "API_KEY" }, user = "someone", timeout_seconds = 45 }
```

<h3 id="action-env">action で利用可能な環境変数</h3>

|環境変数|説明|
|:-----------|:------------|
| MACKEREL_STATUS | 直前の `command` の実行結果です。`OK`, `WARNING`, `CRITICAL`, `UNKNOWN` のいずれかになります。（`max_check_attempts` は考慮されません） |
| MACKEREL_PREVIOUS_STATUS | 前回の `command` の実行結果です。エージェント起動後の初回実行時は空文字列です。2 回目以降は `OK`, `WARNING`, `CRITICAL`, `UNKNOWN` のいずれかになります。（`max_check_attempts` は考慮されません） |
| MACKEREL_CHECK_MESSAGE | 直前の `command` の標準出力結果です。Windows では結果を取得できない場合があります。 |

<h4 id="example-action-env">環境変数の活用例</h4>

下記は `MACKEREL_STATUS` が OK 以外の場合に、`action.command` でサービスの起動や再起動コマンドを実行する action 設定のサンプルです。

Linux

```
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && systemctl restart SERVICE" }
```

Windows

```
action = { command = "if not %MACKEREL_STATUS% == OK ( net start SERVICE )" }
```

サービス名にスペースを含む場合、そのまま記述すると net コマンドでエラーになるため、`action.env` を利用し、環境変数で指定します。

```
action = { command = "if not %MACKEREL_STATUS% == OK ( net start %SERVICE% )", env = { SERVICE = "Sample Service" } }
```

<h2 id="specification">チェックプラグイン仕様</h2>

`command` の終了ステータスに応じて以下のように扱われます。

| 終了ステータス | アラートレベル |
|-----------|------------|
| 0 | OK |
| 1 | WARNING |
| 2 | CRITICAL |
| 0, 1, 2 以外 | UNKNOWN |

プラグインが標準出力に書き出した内容は、ホスト詳細画面やアラート詳細画面に表示されます。パスワードなどの秘匿情報を含めないようにご注意ください。メッセージ長の上限は 1024 文字です。

公式チェックプラグインで利用しているユーティリティライブラリである、[github.com/mackerelio/checkers](https://github.com/mackerelio/checkers) を利用したチェックプラグインの開発方法については、[checkersを利用してチェックプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/checkers) を参照してください。

<h2 id="notification">チェック監視のアラート通知</h2>

アラートが発生した場合と、発生後にアラートのステータスが変化した場合に通知が行われます。ステータスが OK になった場合も含みます。

以下はアラートのステータス変化とその際の通知の有無を示した例です。

| 監視実行回数 | 1 回目 | 2 回目 | 3 回目 | 4 回目 | 5 回目 | 6 回目 |
|---|---|---|---|---|---|---|
| ステータス | CRITICAL | WARNING | WARNING | CRITICAL | CRITICAL | OK |
| アラート通知 | あり | あり | なし | あり | なし | あり |

アラートのステータスやメッセージの内容が変化した場合、その内容はアラート詳細画面にも反映されます。なお、メッセージの内容が変化しただけの場合は通知は行われません。

<h2 id="difference">メトリック監視とチェック監視の違い</h2>

メトリック監視とチェック監視では以下の点で異なります。

- メトリック監視
  - ホスト側はメトリック値を Mackerel に投稿します。その値と閾値との比較やアラート通知は Mackerel 側で行われます。
  - メトリックがグラフとして描画されます。
  - 監視ルールの設定は Web コンソールもしくは Web API で行います。
  - 監視ルールの追加による料金は発生しません。（課金対象はメトリックそのものです）
- チェック監視
  - チェックプラグインを実行するホスト内で OK / NG (`CRITICAL` / `WARNING` / `UNKNOWN`) の判定を行い、その結果を Mackerel に送信します。Mackerel は受け取った結果に応じてアラートを通知します。
  - メトリックが投稿されないためグラフは描画されません。
  - 監視ルールの設定は mackerel-agent の設定ファイルで行います。Web コンソールで設定の追加や変更はできません。
  - 1 つあたり 1 ホストメトリックとしてカウントされます。1 ホストあたりのメトリック数の上限については [料金](https://mackerel.io/ja/pricing) を参照してください。また、上限超過時の仕様については [プラン上限超過時のホスト台数換算について](https://support.mackerel.io/hc/ja/articles/360040109431-%E3%83%97%E3%83%A9%E3%83%B3%E4%B8%8A%E9%99%90%E8%B6%85%E9%81%8E%E6%99%82%E3%81%AE%E3%83%9B%E3%82%B9%E3%83%88%E5%8F%B0%E6%95%B0%E6%8F%9B%E7%AE%97%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6) を参照してください。
- 監視イメージ
  - 【左】メトリック監視【右】チェック監視
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200108/20200108154244.png)


<h2 id="example">チェックプラグインのサンプル</h2>

下記のサンプルは引数で渡したディレクトリ内のファイル数（ディレクトリを除く）をカウントし、ファイル数が閾値を超えたらアラートを発報するチェックプラグインです。

- ファイル数が 50 個を超えた場合に WARNING、100 個を超えた場合に CRITICAL アラートを発報します。
- アラート詳細画面に「file counts: 51（現在のファイル数） over 50」といったメッセージが表示されます。
- Windows 環境の mackerel-agent.conf にパスを記述する際は、パスを区切る `\` に対してエスケープ処理としての `\` が必要です。例として `C:\` は `C:\\` と記述します。

<h3 id="example-shell">シェルスクリプト</h3>

```
#!/bin/bash
FILECOUNT=$(ls -1 $1 | wc -l)

if [ "$FILECOUNT" -gt 100 ]; then
    echo "file counts: $FILECOUNT over 100"
    exit 2
elif [ "$FILECOUNT" -gt 50 ]; then
    echo "file counts: $FILECOUNT over 50"
    exit 1
else
    echo "file counts: $FILECOUNT"
    exit 0
fi
```

mackerel-agent.conf に記述する内容
```
[plugin.checks.filecount]
command = ["/path/filecount.sh", "/path"]
```

<h3 id="example-powershell">PowerShell</h3>

```
$FILECOUNT =  (Get-ChildItem -Path $Args[0] | Where-Object { ! $_.PSIsContainer }).Count

if($FILECOUNT -gt 100) {
    Write-Host "file counts: $FILECOUNT over 100"
    exit 2
} elseif($FILECOUNT -gt 50) {
    Write-Host "file counts: $FILECOUNT over 50"
    exit 1
} else {
    Write-Host "file counts: $FILECOUNT"
    exit 0
}
```

mackerel-agent.conf に記述する内容
```
[plugin.checks.filecount]
command = ["powershell", "-File", "C:\\path\\filecount.ps1", "C:\\path"]
```

<h3 id="example-bat">bat ファイル</h3>

```
@echo off
setlocal
for /f "usebackq" %%i in (`dir %1 /a-d /b ^| find /c /v ""`) do set FILECOUNT=%%i

if %FILECOUNT% gtr 100 (
    echo file counts: %FILECOUNT% over 100
    exit /b 2
) else if %FILECOUNT% gtr 50 (
    echo file counts: %FILECOUNT% over 50
    exit /b 1
) else (
    echo file counts: %FILECOUNT%
    exit /b 0
)
```

mackerel-agent.conf に記述する内容
```
[plugin.checks.filecount]
command = ["C:\\path\\filecount.bat", "C:\\path"]
```

<h2 id="confirmation">チェック監視の確認方法</h2>

チェック監視は設定したホストの詳細画面に以下のように表示されます。

<figure class="figure-image figure-image-fotolife" title="通常時の表示"><center>[f:id:mackerelio:20240501122238p:plain:w320]</center><figcaption>通常時の表示</figcaption></figure>

アラートが発生した際は以下のような表示に変化します。クリックするとアラート詳細画面に遷移します。

<figure class="figure-image figure-image-fotolife" title="アラート発生時の表示"><center>[f:id:mackerelio:20240501122311p:plain:w320]</center><figcaption>アラート発生時の表示</figcaption></figure>

<figure class="figure-image figure-image-fotolife" title="アラート詳細画面"><center>[f:id:mackerelio:20240501122712p:plain]</center><figcaption>アラート詳細画面</figcaption></figure>


[Table]: https://github.com/toml-lang/toml#table
[Inline Table]: https://github.com/toml-lang/toml#inline-table
