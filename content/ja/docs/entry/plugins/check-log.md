---
Title: チェックプラグイン - check-log
Date: 2023-01-27T09:08:27+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889957873691
---

check-logはアプリケーションやミドルウェアなどのサーバー上のログファイルの監視を行うプラグインです。

監視対象のログファイルの出力差分に対して正規表現によるパターンマッチングを行い、アラートを発生させます。

[:contents]


<h2 id="specification">監視仕様</h2>

<h3 id="">監視対象のログ</h3>

check-log プラグインは実行のたびに監視対象ファイルの読み込んだバイト数と inode番号（Linuxのみ）を [Stateファイル](#state-file) に記録し、次に実行する際は前回からの差分のみを監視する仕組みとなっています。

<h3 id="rotation-method">対応するローテーション方式について</h3>

check-log プラグインは、監視対象となるログのローテーション方式として create（ファイル移動後、新たに同名ファイルを作成する）方式に対応しています。copytruncate（ファイルコピー後、コピー元ファイルの中身を削除する）方式の場合、ローテーションの際にコピーされたファイルへの追跡は行わず、内容が削除された元ファイルに対する先頭からの確認のみ行います。

<h2 id="options">指定可能なオプション</h2>

| オプション            | 省略形 | 説明                                                                                      |  デフォルト値  |
| --------------------- | ------ | ----------------------------------------------------------------------------------------- |  ------- |
| --file                | -f     | 監視対象ファイルのパスを指定（glob形式での指定可）[*1](#annotation1)                                          |                   |
| --pattern             | -p     | 検出したいパターンを正規表現で指定 [*2](#annotation2)                                                        |          |
| --exclude             | -E     | 検出から除外するパターンを正規表現で指定 [*2](#annotation2)                                                  |          |
| --warning-over        | -w     | 検出パターンにマッチする行数が指定値を超えたらWarningアラートを発生                       | 0                  |
| --critical-over       | -c     | 検出パターンにマッチする行数が指定値を超えたらCriticalアラートを発生                      | 0                  |
| --warning-level       |        | 検出パターンで抽出した数値が指定値を超えたらWarningアラートを発生                         |                   |
| --critical-level      |        | 検出パターンで抽出した数値が指定値を超えたらCriticalアラートを発生                        |                   |
| --return              | -r     | パターンにマッチしたログ行をアラートで通知する [*3](#annotation3)                                         |                   |
| --search-in-directory |        | 監視対象ファイルがあるディレクトリパスを指定。このオプションを使う場合、監視対象ファイルの指定には`--file`ではなく`--file-pattern`を使用すること |                   |
| --file-pattern        | -F     | 監視対象ファイルを正規表現で指定[*4](#annotation4)<br>Windows環境では`--search-in-directory`との併用を推奨（[設定例](#windows-file-pattern)）                                                          |                   |
| --icase               | -i     | 大小文字を区別せずにマッチングを行う                                                      |                   |
| --state-dir           | -s     | Stateファイルの保存先ディレクトリパスを指定                                               |                   |
| --no-state            |        | Stateファイルを使用せず全てのログを対象とする                                             |                   |
| --encoding            |        | 監視対象ファイルの文字エンコーディングを指定                                              |                   |
| --missing             |        | 監視対象ファイルが存在しなかった場合のアラートレベルを指定 [*5](#annotation5)                                |           UNKNOWN |
| --check-first         |        | ファイルの初回チェック時に内容のチェックを行う [*6](#annotation6)                     |                   |
| --suppress-pattern    |        | 検出パターンをホスト詳細画面に表示しない                                                  |                   |

- <span id="annotation1">*1</span>
  - コマンドラインでの実行時、あるいは mackerel-agent.conf において command 指定で文字列形式を利用している場合は、`--file '/var/log/*.log'` のように glob 形式での指定をシングルクォートで囲む必要があります。
- <span id="annotation2">*2</span>
  - 複数指定すると AND 条件になります。[AND条件で複数のパターンにマッチする場合にアラートを発報する](#AND条件で複数のパターンにマッチする場合にアラートを発報する) もあわせてご覧ください。
  - 正規表現におけるメタ文字を文字列として扱う場合は、メタ文字にエスケープが必要です。詳しくは [正規表現のメタ文字を文字列として扱う](#正規表現のメタ文字を文字列として扱う) をご覧ください。
  - パターンとして日本語（マルチバイト文字）を指定することもできますが、その場合には conf ファイルの文字コード（エンコーディング）が UTF-8 である必要があります。
- <span id="annotation3">*3</span>
  - 検知したログ行をアラートで通知する場合は `--return` オプションを付与します。
  - 1024 文字を超えた内容は mackerel-agent によって切り詰め処理されます。
- <span id="annotation4">*4</span>
  - ディレクトリパスを正規表現で指定することはできません。
- <span id="annotation5">*5</span>
  - 監視対象の指定に`--search-in-directory`および`--file-pattern`を使用している場合は無視されます。
- <span id="annotation6">*6</span>
  - このオプションが付与されていない場合、監視対象ファイルの初回チェック時（ログローテーション直後など）はファイルサイズのみを記録し、内容のチェックをおこないません。
  - [条件にマッチするログ行が出力されたのに検知されない](#条件にマッチするログ行が出力されたのに検知されない) もあわせてご覧ください。

<h3 id="state-file">Stateファイルについて</h3>

`--state-dir`オプションを指定しない場合、OSごとに以下所定の保存先の配下に監視対象のログファイルと同じディレクトリ階層を作り、`{監視対象ファイル}-<hash文字列>.json`の形式でファイルを作成します。たとえば`/var/log`配下のログを監視対象にしている場合は、`/var/tmp/mackerel-agent/check-log/var/log`というディレクトリ配下に保存されます。

- Linuxの場合
  - mackerel-agent 経由で実行した場合
    - `/var/tmp/mackerel-agent/check-log`
  - 手動実行した場合
    - `/tmp/check-log`
- Windowsの場合
  - mackerel-agent 経由で実行した場合
    - `C:\Windows\Temp\check-log`
  - 手動実行した場合
    - ログインユーザの Temp フォルダ配下の check-log フォルダ（Temp フォルダの場所は Windows 環境変数の TEMP を確認）

<h2 id="config">エージェントへの設定例</h2>

<h3 id="config-linux">Linuxサーバーでの監視例</h3>

`/var/log/access.log` に ERROR という文字列を含むログが出力されたことを検知する設定は以下のようになります。

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--return"]
```

上記設定をターミナルなどから直接実行して確認するには、以下のように実行してください。

```
check-log --file /var/log/access.log --pattern "ERROR" --return --no-state
```

<h3 id="config-windows">Windowsサーバーでの監視例</h3>

`C:\log\access.log` に ERROR という文字列を含むログが出力されたことを検知する設定は以下のようになります。Windows 環境のパス区切り文字 `\` は本来 1 つですが、mackerel-agent.conf に設定する際は 2 つ記述する必要があります。

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "C:\\log\\access.log", "--pattern", "ERROR", "--return"]
```

上記設定をコマンドプロンプトなどから直接実行して確認するには、以下のように実行してください。

```
check-log --file "C:\log\access.log" --pattern "ERROR" --return --no-state
```

<h2 id="tips">Tips</h2>

### 発生頻度に対する閾値や除外パターンを指定する

`--warning-over` や `--critical-over` を利用すると、前回の実行からの差分に、指定した行数以上のログが出力された場合にのみアラートを発報できます。
以下は、`/var/log/nginx/error.log` に `ERROR` という文字列が3行以上出力されたらWARNINGに、10行以上出力されたらCRITICALになります。

```toml
[plugin.checks.access_status]
command = ["check-log", "--file", "/var/log/nginx/error.log", "--pattern", "ERROR","--warning-over", "3", "--critical-over", "10", "--return"]
```

また `--exclude` オプションを指定することで、除外パターンを指定することが可能です。
例えば以下の設定ではNginxのアクセスログを監視して、4xxや5xxのエラーの発生をチェックしています。
`--exclude` を指定することで"robots.txt"へのアクセスは除外するようにしています。

```toml
[plugin.checks.access_status]
command = ["check-log", "--file", "/var/log/nginx/access.log", "--pattern", "HTTP/1\.[01]\" [45][0-9][0-9] ", "--exclude", "GET .*?robots\.txt HTTP/1\.[01]", "--return"]
```

### AND条件で複数のパターンにマッチする場合にアラートを発報する

`--pattern` や `--exclude` を複数指定すると AND 条件となり、指定したすべての条件に一致するログをアラートの対象にできます。

以下は PRODUCTION と ERROR という文字列を含むログが検出された場合にアラートが発生します。

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "PRODUCTION", "--pattern", "ERROR", "--return"]
```

### OR条件で何れかにマッチする場合にアラートを発報する

`--pattern` や `--exclude` にパイプ `|` 区切りで複数の条件を指定した場合は OR 条件となり、指定したいずれかの条件に一致するログをアラートの対象にできます。

以下は FATAL もしくは ERROR という文字列を含むログが検出された場合にアラートが発生します。

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL|ERROR", "--return"]
```

### 正規表現のメタ文字を文字列として扱う

`--pattern` や `--exclude` において、正規表現のメタ文字（`[]` などの特殊な意味を持つ文字）を単純な文字列として扱いたい場合は、メタ文字に対してエスケープ処理（メタ文字の前にエスケープ文字 `\` を記述する）が必要です。

以下は [ERROR] という文字列を含むログを検出する場合の設定例です。

`--pattern` の引数をシングルクォーテーション `'` で囲む場合はエスケープ文字を 1 つ記述します。

```
command = ['check-log', '--file', '/var/log/access.log', '--pattern', '\[ERROR\]', '--return']
```

`--pattern` の引数をダブルクォーテーション `"` で囲む場合はエスケープ文字を 2 つ記述します。

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "\\[ERROR\\]", "--return"]
```

`--pattern` の引数をダブルクォーテーション `"` で囲む場合に、ダブルクォーテーション `"` を検出条件の文字に指定したい場合は、エスケープ文字を 3 つ記述します。以下の例では "ERROR" という文字列を含むログを検出します。

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "\\\"ERROR\\\"", "--return"]
```

### 複数のログファイルを対象とする

<h4 id="linux-file-pattern">Linux環境</h4>

単一のファイルではなく複数のファイルを対象としたい場合、`--file` オプションでは glob 形式での指定が、`--file-pattern` オプションでは正規表現での指定ができます。  
以下は `/var/log/*.log` に該当するログファイルを対象とする例です。

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/*.log", "--pattern", "FATAL"]
```

また、`--file-pattern` オプションにより監視対象としたいファイル名の条件を正規表現で指定することも可能です。なお、ディレクトリパスを正規表現で指定することはできません。ディレクトリごとに監視ルールを設定してください。  
以下は `/var/log/access.log.\\d{4}-\\d{2}-\\d{2}` という正規表現に合致する (`/var/log/access.log.2014-09-17` のような) ファイルを対象とする例です。

```toml
[plugin.checks.access_log]
command = ["check-log", "--file-pattern", "/var/log/access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "FATAL"]
```

<h4 id="windows-file-pattern">Windows環境</h4>

Windows 環境において監視対象ファイルの指定に `--file-pattern` による正規表現を用いる場合、ディレクトリの区切り文字 `\` と正規表現のエスケープ処理との競合を回避するために、`--search-in-directory` オプションを併用してください。

```toml
[plugin.checks.access_log]
command = ["check-log", "--search-in-directory", "C:\\log\\", "--file-pattern", "access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "ERROR", "--return"]
```

### Warning アラートのみを発生させたい場合

check-log ではデフォルトで `--warning-over` および `--critical-over` に 0 が指定されます。また、アラートレベルは Critical の方が優先されるため、これらのオプションの指定がない場合、1 行でも条件に一致するログを検出すると Critical アラートが発生します。

もし、すべてのアラートを Warning で発生させたい場合は、`--critical-over` に絶対に超えない数値を指定することで実現できます。

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--critical-over", "9999", "--return"]
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### 条件にマッチするログ行が出力されたのに検知されない

以下のようなケースが多く見受けられますので、設定などの見直しをお願いします。

- `--check-first` オプションが指定されているか確認してください。
  - 未指定の場合、監視対象ファイルの初回チェック時は内容のチェックは行いません。
  - ログローテーションなどでファイルが切り替わった際の初回のチェック時も同様です。
- `--pattern` オプションに指定した検出パターンが正しいか確認してください。
  - 検出パターンは正規表現として評価されるため、半角カッコなど正規表現のメタ文字として解釈される文字を含む場合はエスケープをしてください。
  - [正規表現のメタ文字を文字列として扱う](#正規表現のメタ文字を文字列として扱う) もあわせてご覧ください。
- ローテーション方式としてcreate方式が採用されているか確認してください。
  - copytruncate方式でローテーションされたログでは、ローテーション前にcheck-logが実行されてからローテーションが行われるまでに追記されたログの確認が行えません。
- 該当するログ行が改行されているか確認してください。
  - 行の末尾に改行コードがないログは検出できません。

また、ローテーション後にcheck-logを実行した際のファイルサイズがローテーション前に実行した際のファイルサイズよりも大きい場合、ローテーションを検知できず、ローテーション前にcheck-logが確認したサイズまでに記載されたログに対して検知が行えません。

### LOG UNKNOWN: unexpected end of JSON input のアラートが発報される

check-logプラグインが監視状態を記録しているStateファイルが破損している可能性があります。

Stateファイルを削除もしくはリネームすることで解消が見込めますので、[Stateファイルについて](#state-file) を参考にご対応ください。
またこの対応を行うと監視状態がリセットされるため、初回実行時と同様の挙動となります。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-log
