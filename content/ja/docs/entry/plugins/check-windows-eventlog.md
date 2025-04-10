---
Title: チェックプラグイン - check-windows-eventlog
Date: 2022-12-15T17:45:43+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-windows-eventlog
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889945338732
---

check-windows-eventlog は Windows イベントログの監視を行うプラグインです。指定したパターンに一致するイベントを検出した場合にアラートを発生させます。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション | 省略形 | 説明 | デフォルト値 |
| --- | --- | --- | --- |
| --log | | 検出したいイベントログの種類を指定<br>[監視可能なイベントログの種類](#log-type) を参照 | Application |
| --type | | 検出したいイベントレベルを指定<br>[アラート対象のイベントレベル](#event-type) を参照 |  |
| --source-pattern | | 検出したいイベントソースの指定 |  |
| --source-exclude | | 除外したいイベントソースの指定 |  |
| --message-pattern | | 検出したい文字列パターンを正規表現で指定（AND 条件には非対応） *1 |  |
| --message-exclude | | 除外したい文字列パターンを正規表現で指定（AND 条件には非対応） *1 |  |
| --event-id-pattern | | 検出したいイベント ID の指定（カンマ区切りで複数指定、ハイフンで範囲指定が可能） |  |
| --event-id-exclude | | 除外したいイベント ID の指定（カンマ区切りで複数指定、ハイフンで範囲指定が可能） |  |
| --warning-over | -w | 検出パターンにマッチする行が指定値を超えたら Warning アラートを発生 | 0 |
| --critical-over | -c | 検出パターンにマッチする行が指定値を超えたら Critical アラートを発生 | 0 |
| --status-as | | 監視ステータスの上書き。たとえば `UNKNOWN=CRITICAL` と記述した場合、監視ステータスが UNKNOWN のときは CRITICAL になる。カンマ区切りで複数指定が可能 |  |
| --return | -r | パターンにマッチしたログ行をアラートで通知する（最大1024文字まで） |  |
| --state-dir | -s | State ファイルの保存先ディレクトリパスを指定 | [Stateファイルについて](#state-file) を参照 |
| --no-state | | Stateファイルを使用せず全てのログを対象とする |  |
| --fail-first | | プラグインの設定直後の初回チェック時にアラートを発生させる |  |
| --verbose | | 実行結果を詳細に表示する（デバッグに使用） |  |
| --help | -h | ヘルプを表示 |  |

- *1 複数記述した場合は最後に記述した内容のみ有効になります。

<h3 id="log-type">監視可能なイベントログの種類</h3>

- Application
- Security
- System

<h3 id="event-type">アラート対象のイベントレベル</h3>

| イベントレベル | 監視ステータス |
|---|---|
| Error | Critical |
| Audit Failure | Critical |
| Warning | Warning |
| Information | Warning |

上記以外のイベントレベルには対応していません

<h3 id="state-file">State ファイルについて</h3>

check-windows-eventlog はイベントログの出力差分に対して監視を行うため、最後に読み込んだ EventRecordID を State ファイルに記録しています。

`--state-dir` オプションを指定しない場合、State ファイルは以下のフォルダに `{監視対象のイベントログの種類}-<hash文字列>` の形式で保存されます。

- エージェント経由で実行した場合
  - `C:\Windows\SystemTemp\check-windows-eventlog`
  - mackerel-agent v0.80.0 以前
    - `C:\Windows\Temp\check-windows-eventlog`
- 手動実行した場合
  - ログインユーザの `Temp` フォルダ配下の `check-windows-eventlog` フォルダ（`Temp` フォルダの場所は Windows 環境変数の `TEMP` を確認）

<h2 id="config">エージェントへの設定例</h2>

Application ログの Error イベントのうち、文字列 foo を含み bar を含まないイベントを対象にする。

```
[plugin.checks.check-windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--message-pattern", "foo, "--message-exclude", "bar"]
```

Application ログの Error イベントのうち、イベント ID 900 と 901 を対象にする。

```
[plugin.checks.check-windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--event-id-pattern", "900,901"]
```

Application ログの Error イベントのうち、イベント ID 900 〜 1200 を対象に、1101 のみ除外する。

```
[plugin.checks.check-windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--event-id-pattern", "900-1200", "--event-id-exclude", "1101"]
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### "Perflib:Because the message resource could not be found, the event log message could not be obtained. Please access the target server and check the event log directly." というイベントが発生する

このイベントは `--return` オプションを付与している場合に、パターンに一致したイベントの内容をイベントソースのメッセージファイル（EventMessageFile）から取得できない場合に発生します。その原因として、イベントソースのログがイベントビューアの仕様に合った形式で出力されていない可能性があります。該当のイベントをアラート発生時刻を元にイベントビューアで特定し、イベントソースのログの出力形式ご確認してください。

### Event Log UNKNOWN: strconv.ParseInt: parsing "\x00\x00\x00\x00\x00\x00\x00": invalid syntax というアラートが発生する

State ファイル が破損している可能性があります。監視対象のイベントログに対応した State ファイルを削除することで、アラートの解消が見込めます（State ファイル は次回の監視実行時に再作成されます）。
State ファイルの保存場所については [State ファイルについて](#state-file) をご確認ください。


<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-windows-eventlog
