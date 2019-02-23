---
Title: mackerel-agent仕様
Date: 2014-04-30T18:34:01+09:00
URL: https://mackerel.io/ja/docs/entry/spec/agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722992710
---

この項では[mackerel-agent](https://mackerel.io/my/instruction-agent)の最新バージョンについて解説します。

## Usage

```
mackerel-agent [-conf <config-file>] [options]
```

## 概要

`mackerel-agent` はフォアグラウンドで動作し、

- ホストの情報をMackerelに登録、
- ホスト上で定期的（1分毎）にリソース情報を収集し、Mackerelに投稿、
- また、定期的（1時間毎）にホストの情報を更新します。

投稿されたホスト情報、リソース情報はMackerelのウェブインターフェースで確認できます。

<h2 id="config-file">設定ファイル</h2>

設定ファイルはデフォルトで `/etc/mackerel-agent/mackerel-agent.conf` が参照されます。記述例としては以下のようになります。

```conf
apikey = "APIKEY"
pidfile = "/path/to/pidfile"
root = "/var/lib/mackerel-agent"

cloud_platform = "ec2"

verbose = false
silent = false

display_name = "My Host"

roles = [ "My-Service:app", "Another-Service:db" ]

http_proxy = "http://localhost:8080"

diagnostic = true

include = "/etc/mackerel-agent/conf.d/*.conf"

[host_status]
on_start = "working"
on_stop  = "poweroff"

[filesystems]
ignore = "/dev/ram.*"
use_mountpoint = true

[plugin.metrics.vmstat]
command = ["ruby", "/etc/sensu/plugins/system/vmstat-metrics.rb"]

[plugin.metrics.mysql]
command = ["ruby", "/path/to/your/plugin/mysql.rb"]
env = { MYSQL_USERNAME = "USERNAME", MYSQL_PASSWORD = "PASSWORD" }

[plugin.checks.heartbeat]
command = ["heartbeat.sh"]
env = { "ES_HOSTS" = "10.45.3.2:9220,10.45.3.1:9230" }
action = { command = "cardiac_massage", user = "doctor", env = { SAMPLE_KEY_1 = "VALUE_1", SAMPLE_KEY_2 = "VALUE_2" } }

[plugin.checks.ssh]
command = ["ruby", "/path/to/check-ssh.rb"]

[plugin.metadata.packages]
command = ["perl", "/path/to/packages.pl"]
```

<h3 id="config-file-format">設定ファイルの書式について</h3>
設定ファイルの書式には[TOML][]形式を採用しています。そのため、このページで記載している点以外の一般的な記述の仕方については、[TOML][]形式に準じます。


<h3 id="config-file-encoding">ファイルエンコーディングについて</h3>
設定内容にマルチバイト文字を含む場合は、ファイルエンコーディングが `UTF-8` である必要があります。


<h3 id="config-file-editing">設定ファイル編集時の注意点について</h3>
設定ファイルはエージェントの起動時に読み込まれます。そのため、編集後にその内容を反映させるためにはエージェントを再起動する必要があります。


<h3>各設定項目について</h3>
`apikey` 以外の設定項目はオプショナル項目です。そのため、デフォルト値のままでよい場合は明示的な指定は必要ありません。

<h4 id="config-file-apikey">apikey</h4>
エージェントが Mackerel サービスとの通信の際に用いる API キーを指定します。APIキーは、[APIキー設定タブ](https://mackerel.io/my?tab=apikeys)から新たなAPIキーを発行したり、発行済のAPIキーを失効させることができます。


<h4 id="config-file-pidfile">pidfile</h4>
mackerel-agent の pidfile を出力する場所を指定できます。指定しない場合、`/var/run` 以下に出力されます。


<h4 id="config-file-root">root</h4>
mackerel-agent のルートディレクトリを指定できます。このディレクトリ内に、Mackerelがホストを一意に識別するためのホストIDが記録されたファイルが出力されます。


<h4 id="config-file-cloudplatform">cloud_platform</h4>
AWS EC2やAzure VM、GCP上でmackerel-agentを実行する場合、mackerel-agentは自身が実行されているIaaS環境を自動的に検出します。これらのIaaS環境にあることを識別できた場合、mackerel-agentはIaaSのメタ情報を収集するほか、AWSインテグレーションやAzureインテグレーションとの連携に必要な識別子(`custom_identifier`)をホストに対して適用します。

通常、この検出は自動的に行われますが、設定ファイルに `cloud_platform` オプションを指定することで明示的にプラットフォームを指定することができます。有効な値は、 `"auto"`(デフォルト) `"ec2"` `"gce"` `"azurevm"` `"none"` の5種類です。

Dockerコンテナを利用する場合など、一つのIaaSインスタンス上で複数のmackerel-agentを起動して別々のホストとして認識させたい場合は、インスタンス本体に対応するmackerel-agent以外の設定ファイルではこのオプションを `"none"` にする必要があります。


<h4 id="config-file-verbose">verbose</h4>
`verbose = true` とすることで、DEBUGログの出力を有効にすることができます。


<h4 id="config-file-silent">silent</h4>
`silent = true` とすることで、WARNレベル以下のログ出力を抑制することができます。


<h4 id="config-file-displayname">display_name</h4>
設定ファイルによる指定で、ホストに対して任意の別名を設定することが出来ます。すでに管理名が設定されているホストの場合は新しい名前に更新されます。

また、設定ファイルから指定を削除した場合も管理名は設定されたままとなります。ホスト設定ページ（各ホスト詳細画面内、ホスト名右の歯車アイコン）から削除することが出来ます。


<h4 id="config-file-roles">roles</h4>
ホストのサービス、ロールを設定できます。エージェント起動時、設定ファイルで指定されたサービスやロールが存在しない場合は、新たに作成されます。また、ウェブ画面側での変更がエージェントの設定ファイルに同期されることはありません。

**例: My-ServiceサービスのappロールおよびAnother-Serviceサービスのdbロールを紐付けたい場合**

```config
# /etc/mackerel-agent/mackerel-agent.conf
roles = [ "My-Service:app", "Another-Service:db" ]
```


<h4 id="config-file-proxy">proxy</h4>
このオプションを指定することで、エージェントが通信に利用する HTTP Proxy を設定することができます。このオプションを利用することにより、監視対象サーバーが直接のインターネット通信をおこなえないようなネットワーク環境でも、プロキシサーバーを経由してのサーバー監視をおこなうことが可能です。

**例: 経由させたいプロキシサーバーが localhost:8080 で提供されている場合**

```config
# /etc/mackerel-agent/mackerel-agent.conf
http_proxy = "http://localhost:8080"
```

また、環境変数 `HTTP_PROXY` にも対応しています。例えば、次のように指定することで、HTTP Proxy経由のアクセスにすることができます。

```sh
% HTTP_PROXY=http://localhost:8080 mackerel-agent
```

yum/rpmの場合 `/etc/sysconfig/mackerel-agent` 、apt/debの場合は `/etc/default/mackerel-agent` ファイルに環境変数の設定を追記することでmackerel-agentに適用させることができます。例えば次のように指定します。

```
HTTP_PROXY="http://localhost:8080/"
```


<h4 id="config-file-diagnostic">diagnostic</h4>
このオプションを指定することで、エージェント自身のメモリ使用状況を収集しカスタムメトリックとして投稿します（診断モード。デフォルトは `false` ）。

エージェントの起動時オプションとしても指定可能です。

```
mackerel-agent -diagnostic
```

診断モードを有効にすることにより、以下のメトリックの収集が行われます。

- `custom.agent.memory.alloc`
- `custom.agent.memory.sys`
- `custom.agent.memory.heapAlloc`
- `custom.agent.memory.heapSys`

取得できる値に関しての詳細は[https://golang.org/pkg/runtime/#MemStats](https://golang.org/pkg/runtime/#MemStats)をご参照ください。


<h4 id="config-file-include">include</h4>
mackerel-agent 設定項目を他の設定ファイルに記述し、それを `include` オプションを指定することにより読み込ませることが可能です。

- `/etc/mackerel-agent/conf.d/*.conf` のように、ファイルグロブを指定できます。
- 文字列、真偽値、配列の設定項目（`verbose` や `roles`）に関して、後から読み込まれた設定ファイルに値が存在する場合、上書きされます。
- 複数の設定ファイルがマッチするとき、それらが読み込まれる順序は不定です。
- 読み込まれた設定ファイル中の `include` 設定項目は、無視されます。


<h4 id="config-file-hoststatus">host_status</h4>
エージェント起動時の、そのホストのステータスを設定できます。エージェントの起動時、終了時で別々のステータスを指定することができます。

例えば以下のように指定することで、エージェントが起動したときに、ホストのステータスが `working` に設定され、エージェントが正常終了した際に `poweroff` に設定されます。

```config
# /etc/mackerel-agent/mackerel-agent.conf
[host_status]
on_start = "working"
on_stop  = "poweroff"
```

指定できるステータスは、`working` `standby` `maintenance` `poweroff` です。ホストステータスによる監視や通知の仕様については、[監視・通知を設定する](https://mackerel.io/ja/docs/entry/howto/alerts#host-statuses)を参照してください。


<h4 id="config-file-filesystems">filesystems</h4>
<h5>ignore</h5>
特定のファイルシステムからのメトリック収集を除外できます。

以下のように正規表現を用いて指定することで、指定されたファイルシステムからのメトリックは収集されなくなります。

```config
# /etc/mackerel-agent/mackerel-agent.conf
[filesystems]
ignore = "/dev/ram.*"
```

<h5>use_mountpoint</h5>
設定ファイルに以下のように指定することで、マウントポイントごとに filesystem メトリックを取得することが可能です。

```config
# /etc/mackerel-agent/mackerel-agent.conf
[filesystems]
use_mountpoint = true
```


<h4 id="config-file-custommetrics">[plugin.metrics.{name}]</h4>
`plugin.metrics.{name}` セクションで、任意のメトリックを取得、投稿するための設定を記述できます。

`command` で指定されたコマンドをエージェントが実行し、その結果を標準出力としてエージェントが受け取り、Mackerelサービスに投稿します。
そのための仕組みとして、任意のユーザースクリプトの他、プラグインを利用することが可能です。詳しくは[ユーザ定義のメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)を参照してください。


<h4 id="config-file-customchecks">[plugin.checks.{name}]</h4>
`plugin.checks.{name}` セクションで、任意のチェック監視処理を実行、結果を投稿するための設定を記述できます。
「チェック監視処理」とは、「特定のプロセスが起動しているかどうか」「監視対象のログファイル内に問題のある出力がされているかどうか」といった種類の監視です。この設定により投稿された結果はグラフとして描画はされません。

そのための仕組みとして、任意のユーザースクリプトの他、プラグインを利用することが可能です。詳しくは[チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)を参照してください。


<h4 id="config-file-metadata">[plugin.metadata.{name}]</h4>
`plugin.metadata.{name}` セクションで、任意の処理を実行しその結果を[メタデータ](https://mackerel.io/ja/docs/entry/howto/metadata)に登録するための設定を記述できます。詳しくは[メタデータを利用する](https://mackerel.io/ja/docs/entry/howto/metadata)を参照してください。

<h2 id="option"> 起動オプション</h2>

以下の起動オプションが指定できます。設定ファイルを同じ項目を指定した場合、起動オプションが優先されます。

- `-apikey=API_KEY` Mackerelのウェブ上で確認できるAPIキーです。この値で、ホストがどのオーガニゼーションに所属するかを識別します。（非推奨。バージョン0.5.1より設定ファイルに記述することを推奨しています。）
- `-conf=/etc/mackerel-agent/mackerel-agent.conf` 設定ファイルへのパスです。未指定の場合、デフォルトで`/etc/mackerel-agent/mackerel-agent.conf`が読み込まれます。
- `-pidfile=/var/run/mackerel-agent.pid` PIDファイルへのパスです。
- `-root=/var/lib/mackerel-agent` mackerel-agentの状態を記録するディレクトリへのパスです。現在はホストを識別するidファイルのみが置かれます。
- `-role=<service>:<role>` ホストに割り当てるロールおよびサービスを指定します。
- `-once` 一度だけメトリックの収集を実行して標準出力に表示します。投稿は行われません。
- `-diagnostic` エージェント自身のメトリックを収集して投稿します。

<h2 id="faq">FAQ</h2>

### エージェントはどうやってホストを判別していますか?

`/var/lib/mackerel-agent/id` にホストIDが保存されており、このIDでどのホストか判別します。ホスト名は判別に利用されません。（設定ファイルは起動オプションでrootを変更している場合、そのroot以下にidファイルが作成されます。)

### エージェントが起動しません

`/var/lib/mackerel-agent/id` に正しくないIDが保存されている場合、正しく起動しません。`/var/lib/mackerel-agent/id` を削除してから起動してみてください。（新規ホストとして認識されます）（設定ファイルは起動オプションでrootを変更している場合、そのroot以下にidファイルが作成されます。)

### 設定に日本語（マルチバイト文字）は使用可能ですか？

使用可能です。ただし、conf ファイルの文字コード（エンコーディング）が `UTF-8` である必要があるので注意してください。


## サブコマンド

エージェントはいくつかのサブコマンドを持ちます。

### retire

```
mackerel-agent retire [-conf <config-file>] [-force] [options]
```

ホストを退役させます。実行時に確認プロンプトが表示されます。 `-force` オプションをつけることで、確認プロンプトを出さずに退役処理を行うことができます。

### configtest

```
mackerel-agent configtest
```

設定ファイル（ `mackerel-agent.conf` ）のシンタックスチェックをおこなうことができます。

## initスクリプト用設定ファイル

yum/rpmの場合 `/etc/sysconfig/mackerel-agent` 、apt/debの場合は `/etc/default/mackerel-agent` ファイルに環境変数の設定を追記することでmackerel-agentに適用させることができます。以下の様な変数が設定可能です。

### OTHER_OPTS

mackerel-agentに追加したいオプションを指定します。例えば `OTHER_OPTS="-diagnostic -verbose"` のように指定すると診断モードが有効になるとともにDEBUGログが出力されます

### AUTO_RETIREMENT

この変数に0以外の文字列を指定すると、OSシャットダウン時(正確には `/etc/init.d/mackerel-agent stop` 時)にホストが自動的に退役されます。

**注意** 手動で `/etc/init.d/mackerel-agent stop` を打った場合でもこの指定がされていた場合にはホストは退役されてしまいます。エージェントのバージョンアップ時など、エージェントをstop/startさせたい場合には、`/etc/init.d/mackerel-agent reload` を使うようにして下さい。

[TOML]: https://github.com/mojombo/toml

## Windowsシステム環境変数

### MACKEREL_AUTO_RETIREMENT

Windowsのシステム環境変数で、この変数に0以外の文字列を指定すると、OSシャットダウン時にホストが自動的に退役されます。サービスの停止では退役処理は実行されません。
