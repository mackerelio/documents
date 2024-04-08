---
Title: チェックプラグイン - check-log
Date: 2023-01-27T09:08:27+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889957873691
CustomPath: plugins/check-log
---

check-logはアプリケーションやミドルウェアなどのサーバー上のログファイルの監視を行うプラグインです。

監視対象のログファイルの出力差分に対して正規表現によるパターンマッチングを行い、アラートを発生させます。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション            | 省略形 | 説明                                                                                      |  初期値  |
| --------------------- | ------ | ----------------------------------------------------------------------------------------- |  ------- |
| --file                | -f     | 監視対象ファイルのパスを指定（glob形式での指定可）                                        |                   |
| --pattern             | -p     | 検出したいパターンを正規表現で指定 *1                                                        |          |
| --exclude             | -E     | 検出から除外するパターンを正規表現で指定 *1                                                  |          |
| --warning-over        | -w     | 検出パターンにマッチする行数が指定値を超えたらWarningアラートを発生                       |                   |
| --critical-over       | -c     | 検出パターンにマッチする行数が指定値を超えたらCriticalアラートを発生                      |                   |
| --warning-level       |        | 検出パターンで抽出した数値が指定値を超えたらWarningアラートを発生                         |                   |
| --critical-level      |        | 検出パターンで抽出した数値が指定値を超えたらCriticalアラートを発生                        |                   |
| --return              | -r     | パターンにマッチしたログ行をアラートで通知する *2                                         |                   |
| --search-in-directory |        | 監視対象ファイルがあるディレクトリパスを指定（Windows環境において`--file-pattern`と併用） |                   |
| --file-pattern        | -F     | 監視対象ファイルを正規表現で指定                                                          |                   |
| --icase               | -i     | 大小文字を区別せずにマッチングを行う                                                      |                   |
| --state-dir           | -s     | Stateファイルの保存先ディレクトリパスを指定                                               |                   |
| --no-state            |        | Stateファイルを使用せず全てのログを対象とする                                             |                   |
| --encoding            |        | 監視対象ファイルの文字エンコーディングを指定                                              |                   |
| --missing             |        | 監視対象ファイルが存在しなかった場合のアラートレベルを指定                                |           UNKNOWN |
| --check-first         |        | ファイルの初回チェックのタイミングで全ての内容に対してチェックを行う                      |                   |
| --suppress-pattern    |        | 検出パターンをホスト詳細画面に表示しない                                                  |                   |

- *1 複数記述すると AND 条件になります（`"--pattern", "A", "--pattern", "B"`）。
- *2 mackerel-agentによって1024文字を超えた内容は切り詰め処理されます。

<h3 id="state-file">Stateファイルについて</h3>

check-log プラグインはログファイルの出力差分に対して監視を行うため、ファイルごとに読み込んだバイト数、inode番号（Linuxのみ）をStateファイルに記録しています。

`--state-dir`オプションを指定しない場合、OSごとに以下所定の保存先の配下に監視対象のログファイルと同じディレクトリ階層を作り、`{監視対象ファイル}-<hash文字列>.json`の形式でファイルを作成します。

- Linuxの場合
  - `/var/tmp/mackerel-agent/check-log`もしくは`/tmp/check-log`
- Windowsの場合
  - `C:\Windows\Temp\check-log`

<h3 id="rotation-method">対応するローテーション方式について</h3>

check-log プラグインは、監視対象となるログのローテーション方式としてcreate(ファイル移動後、新たに同名ファイルを作成する)方式に対応しています。
copytruncate(ファイルコピー後、コピー元ファイルの中身を削除する)方式の場合、ローテーションの際にコピーされたファイルへの追跡は行わず、内容が削除された元ファイルに対する先頭からの確認のみ行います。

<h2 id="config">エージェントへの設定例</h2>

<h3 id="config-linux">Linuxサーバーでの監視例</h3>

/var/log/access.log に `ERROR` が出力されたことを検知する設定は以下のようになります。

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR"]
```

検知したログ行をアラートで通知する場合は `--return` オプションを付与します。

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--return"]
```

監視対象のファイルの初回チェック時は内容のチェック（検出パターンのマッチ）は行いません。初回チェック時も内容のチェックを行う場合は `--check-first` オプションを付与します。

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--return", "--check-first"]
```

上記設定をターミナルなどから直接実行して確認するには、以下のように実行してください。

```
check-log --file /var/log/access.log --pattern "ERROR" --return --check-first
```

<h3 id="config-windows">Windowsサーバーでの監視例</h3>

C:\log\access.log に `ERROR` が出力されたことを検知する場合の設定は以下のようになります。

```
[plugin.checks.access_log]
command = ["check-log", "--file", "C:\\log\\access.log", "--pattern", "ERROR"]
```

Windows環境において `--file-pattern` によるファイル指定に正規表現を用いる場合、ディレクトリの区切り文字 `\` と正規表現のエスケープ処理との競合を回避するため、 `--search-in-directory` オプションにディレクトリパスを指定してください。

`C:\log\access.log.{yyyy}-{mm}-{dd}`のような形式のファイルを正規表現で指定する場合は以下のようになります。

```
[plugin.checks.access_log]
command = ["check-log", "--search-in-directory", "C:\\log\\", "--file-pattern", "access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "ERROR"]
```

なお、ディレクトリパスを正規表現で指定することはできません。ディレクトリごとに監視ルールを設定してください。

<h2 id="tips">Tips</h2>

### 複数のパターンにAND条件でマッチする場合にアラートを発報する

`--pattern` オプションを複数指定すると、すべてのキーワードを含むログ行を検知した場合にアラートが発報されます。

以下は `PRODUCTION` と `ERROR` を含むことを条件とする場合の指定です。

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "PRODUCTION", "--pattern", "ERROR"]
```

### OR条件で何れかにマッチする場合にアラートを発報する

`--pattern` オプションに指定した条件は正規表現として評価されます。

例えば、 `FATAL` もしくは `ERROR` を含むログ行を検出したい場合は以下のようにパイプ`|`区切りで指定します。

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL|ERROR"]
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### 条件にマッチするログ行が出力されたのに検知されない

以下のようなケースが多く見受けられますので、設定などの見直しをお願いします。

- `--check-first` オプションが指定されているか確認してください。
  - 未指定の場合、監視対象ファイルの初回チェック時は内容のチェックは行いません。
  - ログローテーションなどでファイルが切り替わった際の初回のチェック時も同様です。
- `--pattern` オプションに指定した検出パターンが正しいか確認してください。
  - 検出パターンは正規表現として評価されるため、半角カッコなど正規表現のメタ文字として解釈される文字を含む場合はエスケープをしてください。
- ローテーション方式としてcreate方式が採用されているか確認してください。
  - copytruncate方式でローテーションされたログでは、ローテーション前にcheck-logが実行されてからローテーションが行われるまでに追記されたログの確認が行えません。

また、ローテーション後にcheck-logを実行した際のファイルサイズがローテーション前に実行した際のファイルサイズよりも大きい場合、ローテーションを検知できず、ローテーション前にcheck-logが確認したサイズまでに記載されたログに対して検知が行えません。

### LOG UNKNOWN: unexpected end of JSON input のアラートが発報される

check-logプラグインが監視状態を記録しているStateファイルが破損している可能性があります。

Stateファイルを削除もしくはリネームすることで解消が見込めますので、[Stateファイルについて](#state-file) を参考にご対応ください。
またこの対応を行うと監視状態がリセットされるため、初回実行時と同様の挙動となります。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-log

