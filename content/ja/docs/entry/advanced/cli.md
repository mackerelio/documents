---
Title: CLIツール mkr を使う
Date: 2015-06-19T12:03:19+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/cli
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450097006151
---

CLIツールmkrを利用することで、ホストのステータスをまとめて変更するような操作や、手順をスクリプトに組込み自動化することができるようになります。

mkrはGitHubで公開しております。
https://github.com/mackerelio/mkr

[:contents]

## mkr の導入

### Linux 系 OS の場合

mkr を利用するためはインストールが必要です。yum や apt を利用してインストールする場合、あらかじめ Mackerel の package リポジトリを追加する必要があります。Mackerel の package リポジトリは、mackerel-agent のインストールコマンドを実行した際に自動で追加されます。mackerel-agent のインストール方法については以下のヘルプを参照してください。

- [Amazon Linuxにmackerel-agentをインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/amazon-linux)
- [Red Hat Enterprise Linux派生OSにインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/rpm)
- [Ubuntu / Debian にインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/deb)

#### yumを利用

```
% yum install mkr
```

#### aptを利用

```
% apt-get install mkr
```

#### brewを利用

```
% brew tap mackerelio/mackerel-agent
% brew install mkr
```

#### goでビルド

```
% go install github.com/mackerelio/mkr@latest
```

### Windows の場合

mackerel-agent をインストールすることで利用可能になります。

[https://mackerel.io/ja/docs/entry/howto/install-agent/msi:embed:cite]

### API キーの設定

mkr の利用には API キーの設定が必要です。mackerel-agent がインストールされた環境においては、デフォルトで mackerel-agent.conf に記述された API キーが使用されます。mackerel-agent がインストールされていない環境で利用する場合は、下記のように API キーを環境変数として設定する必要があります。

Linux 系 OS の場合

```
export MACKEREL_APIKEY=<API key>
```

Windows の場合

```
set MACKEREL_APIKEY=<API key>
```

## 使い方

詳しい使い方は https://github.com/mackerelio/mkr をご参照ください。

### ホスト関連

例えばhostIdを指定して、そのホストの情報を取得できます。
```
mkr status <hostId>
```

```json
$ mkr status 2eQGEaLxibb
{
    "id": "2eQGEaLxibb",
    "name": "myproxy001",
    "status": "standby",
    "roleFullnames": [
        "My-Service:proxy"
    ],
    "isRetired": false,
    "createdAt": "2014-11-15T21:41:00+09:00"
}
```

またhostIdを指定してステータス変更を行なったり、

```
mkr update --status maintenance <hostIds>...
```

ホスト一覧を取得することが可能です。
```
mkr hosts --service My-Service --role proxy
```

これらを組み合わせて、指定したサービスとロールのホストのステータスをまとめて変更できます。

```
mkr update --st working $(mkr hosts -s My-Service -r proxy | jq -r '.[].id')
```

そのほか、任意のスタンダードホストを作成することもできます。例えばmackerel-agentやクラウドインテグレーションなどが利用できないネットワーク機器をホストとして作成し、別途APIを使ってそのホストのメトリックやチェック監視の結果を投稿するといった使い方が考えられます。

```
mkr create --status working --roleFullname 'My-Service:Router' --memo 'ゲートウェイルーター。外部から mkr を利用してメトリック投稿している' MyServiceRouter01
```

### メトリックの名前の取得

ホストやサービスが提供するメトリックの名前は、metric-namesサブコマンドで取得できます。

```
mkr metric-names -H <hostId>
mkr metric-names -s <serviceName>
```

#### 実行例
- ホストメトリックの名前の取得
```
% mkr metric-names -H 2eQGEaLxibb
[
    "cpu.guest.percentage",
    "cpu.idle.percentage",
    "cpu.iowait.percentage",
    ...
]
```

- サービスメトリックの名前の取得
```
% mkr metric-names -s myservice
[
    "Sample.foo"
    "Sample.bar"
    ...
]
```

ホストメトリックの名前の一覧は、statusサブコマンドに `-v` を付けることでも取得できます。

```
% mkr status -v 2eQGEaLxibb
{
    "id": "2eQGEaLxibb",
    ...
    "metrics": [
        "cpu.guest.percentage",
        ...
        "memory.used"
    ]
}
```

### メトリック取得

mkrでは metrics サブコマンドでメトリックを取得できます。
例えば hostId を指定することで特定のホストのメトリックを、あるいはサービス名を指定することで特定のサービスのメトリックを取得できます。

```
mkr metrics --host <hostId> --name <name> --from <epoch seconds> --to <epoch seconds>  
```

```json
% mkr metrics --host 39gQhpp3Wpj --name cpu.user.percentage --from 1508909640 --to 1508909760
[
    {
        "time": 1508909640,
        "value": 14
    },
    {
        "time": 1508909700,
        "value": 12
    },
    {
        "time": 1508909760,
        "value": 27
    }
]
```

`--from`, `--to` でエポック秒を指定することで、取得するメトリックの範囲を指定できます。
`--from` は指定しない場合は0、`--to` は指定しない場合は現在時刻となります。

```json
% mkr metrics --service myservice --name Sample.foo
[
    {
        "time": 1508912100,
        "value": 30
    }
]
```

### メトリック投稿

mkrではthrowサブコマンドでメトリックを投稿できます。ホストIDを指定することでホストメトリックを、サービス名を指定することでサービスメトリックを投稿できます。
throwサブコマンドは、標準入力で投稿するメトリックを受け取ります。メトリックのフォーマットについては、[ホストのカスタムメトリックを投稿する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)内、[メトリックの投稿 - ホストのカスタムメトリックを投稿する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/advanced/custom-metrics#post-metric)をご確認ください。

以下は、ホストID `2eQGEaLxibb` のホストメトリック `custom.example.throwA` に値1を投稿する例です。

```
% echo -e "example.throwA\t1\t$(date +%s)" | mkr throw --host 2eQGEaLxibb
```

成功すると、以下のようなメッセージが表示されます。
```
    thrown 54EwSZbNexW 'custom.example.throwA       1.000000        1711596750'
```

以下は、サービス `myservice` のサービスメトリック `example.throwB` に値2を投稿する例です。

```
% echo -e "example.throwB\t2\t$(date +%s)" | mkr throw --service myservice
```

成功すると、以下のようなメッセージが表示されます。
```
    thrown myservice 'example.throwB   2.000000        1711596974'
```

同時に複数のメトリックを投稿することもできます。
例として、`custom.example.throwC`に対して値1、`custom.example.throwD`に対して値2をそれぞれ投稿する場合は、以下のように`\n`を用いて、改行された状態の出力を用意します。

```
% echo -e "example.throwC\t1\t$(date +%s)\nexample.throwD\t2\t$(date +%s)"
example.throwC  1       1713273764
example.throwD  2       1713273764
```

この出力結果を `mkr throw` コマンドの標準入力に引き渡すことで、`custom.example.throwC` と `custom.example.throwD` の2メトリックが同時に投稿されます。

```
% echo -e "example.throwC\t1\t$(date +%s)\nexample.throwD\t2\t$(date +%s)" | mkr throw --host 2eQGEaLxibb
```

[メトリックプラグイン一覧 - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/plugins/metric-plugins-list)に記載されている公式メトリックプラグインのような、mackerel-agentからメトリック投稿が可能なメトリックプラグインは、`mkr throw`が受け付けるフォーマットでメトリックを出力するため、以下のように実行結果を標準入力に引き渡すことで投稿が可能です。

```
% mackerel-plugin-mysql | mkr throw --host 2eQGEaLxibb
```


### 監視ルール

mkrではmonitorsサブコマンドで監視ルールを操作できます。サブコマンドは、pull/diff/pushの3種類あります。サブコマンドを指定しない場合、監視ルール一覧が表示されます。

- `pull`
  - Mackerelから監視ルール一覧を取得し、ローカルファイルに保存します。
- `diff`
  - Mackerelに設定されている監視ルール一覧と、ローカルファイルとの差分を表示します。
- `push`
  - ローカルファイルの監視ルールの設定をMackerelに反映します。

jsonフォーマットについては、[API仕様の「監視ルールの登録」](https://mackerel.io/ja/api-docs/entry/monitors#create)を参照してください

#### push時の監視ルールの同一性判定ロジック

`pull`する際はMackerelが付与した`id`が必ず含まれます。
`push`する場合は`id`が含まれないルールも許容するために以下のロジックで監視ルールの同一判定を行います。

- `id`を含む .. `id`で同一判定
- `id`を含まず`name`を含む .. `name`で同一判定
  - 同じ`name`を持つ監視ルールが存在した場合、`id`でのみ同一判定を行います。そのため全ての監視ルールに`id`が含まれる必要があります。またWarningメッセージを出力します。
- `id`も`name`も含まないルールがある場合はエラーとなります。

#### 実行例

- ローカルファイルとMackerel間の差分がない場合の`diff`
```
% mkr monitors diff
Summary: 0 modify, 0 append, 0 remove
```

- ローカルファイルとMackerel間の差分がある場合の`diff`
```diff
% mkr monitors diff
Summary: 1 modify, 1 append, 1 remove

  {
   "name": "Filesystem %",
   "type": "host",
   "metric": "disk%",
   "operator": ">",
-  "warning": 95.000000,
+  "warning": 96.000000,
   "critical": 99.000000,
   "duration": 3,
   "url": "",
    "scopes": [
    ],
    "excludeScopes": [
    ],
  },
- {
-   "id": "<id>",
-   "name": "loadavg5",
-   "type": "host",
-   "metric": "loadavg5",
-   "operator": ">",
-   "warning": 1.000000,
-   "critical": 5.000000,
-   "duration": 3,
-   "url": "",
-   "scopes": [
-     "My-Service:proxy"
-   ],
-   "excludeScopes": [
-   ],
- },
+ {
+   "name": "loadavg",
+   "type": "host",
+   "metric": "loadavg5",
+   "operator": ">",
+   "warning": 1.000000,
+   "critical": 5.000000,
+   "duration": 5,
+   "url": "",
+   "scopes": [
+     "My-Service:proxy"
+   ],
+   "excludeScopes": [
+   ],
+ },
```

- `push`の例
```json
% mkr monitors push --dry-run -F monitors_new.json
      info Create a new rule.
 {
   "name": "loadavg",
   "type": "host",
   "metric": "loadavg5",
   "operator": ">",
   "warning": 1.000000,
   "critical": 5.000000,
   "duration": 5,
   "url": "",
   "scopes": [
     "My-Service:proxy"
   ],
   "excludeScopes": [
   ],
 },
      info Delete a rule.
 {
   "id": "<id-1>",
   "name": "loadavg5",
   "type": "host",
   "metric": "loadavg5",
   "operator": ">",
   "warning": 1.000000,
   "critical": 5.000000,
   "duration": 3,
   "url": "",
   "scopes": [
     "My-Service:proxy"
   ],
   "excludeScopes": [
   ],
 },
      info Update a rule.
 {
   "id": "<id-2>",
   "name": "Filesystem %",
   "type": "host",
   "metric": "disk%",
   "operator": ">",
   "warning": 96.000000,
   "critical": 99.000000,
   "duration": 3,
   "url": "",
   "scopes": [
   ],
   "excludeScopes": [
   ],
 },
```

### アラート関連

`mkr alerts` サブコマンドでは、アラートに関する操作が可能です。

`mkr alerts` および `mkr alerts list` では、デフォルトでは発生中のアラートを、`--with-closed` (`-w`) オプションを利用することで発生中だけのアラートだけでなくクローズ済みのアラートも含めた一覧を取得できます。サービスやホストステータスによるフィルターも可能です。  
`mkr alerts` と `mkr alerts list` の違いはその表示形式です。 `mkr alerts` ではAPI同様にホストIDや監視ルールIDなどを含めたjson形式で出力され、`mkr alerts list` ではホスト名や監視ルール名などが展開された Human-Readable な形式で表示されます。

```
% mkr alerts -w
[
    {
        "id": "5uN9FBr6JVw",
        "status": "WARNING",
        "monitorId": "4TG6xGK64PW",
        "type": "host",
        "hostId": "4RXBJ9SQw67",
        "value": 73.68399467417471,
        "openedAt": 1750675925
    },
    {
        "id": "5pjkT5GqhPs",
        "status": "CRITICAL",
        "monitorId": "4TG93ajat7Q",
        "type": "check",
        "hostId": "4RNuLWQKJh1",
        "message": "Procs CRITICAL: \nFound 0 matching processes; cmd /w3wp/",
        "openedAt": 1740972447
    }
]

% mkr alerts list -w
5uN9FBr6JVw 2025-06-23 19:52:05 WARNING  Windows メモリ割合監視 memory% 73.68 > 70.00 EC2AMAZ-1S800NC standby [Win-Demo:DB]
5pjkT5GqhPs 2025-03-03 12:27:27 CRITICAL  Procs CRITICAL: ... EC2AMAZ-GELUS5G working [Win-Demo:Web]
```

`mkr alerts` および `mkr alerts list` のデフォルト設定では、最新から最大100件までのアラートを取得できますが、
`mkr alerts -l 300` のように `-l` (`--limit`) オプションを利用することで、より多くのアラートを取得できます。

`mkr alerts close` コマンドでは、発生中のアラートをクローズできます。  
`--reason` (`-r`) オプションを利用することで、コンソール上からクローズする際と同様にメモを追加できます。

```
% mkr alerts close 5wfKdWBXT8W --reason "前回の対応時のクローズ漏れのため手動クローズ"
Alert closed 5wfKdWBXT8W
```

`mkr alerts logs` コマンドでは、指定したIDのアラートに関する詳細情報を取得できます。
結果はjson形式で返ります。`--jq` オプションを使うと、jq文法で出力のフィルタリング・整形が可能です。

```
% mkr alerts logs 5wDea1jMahU
[
    {
        "id": "5wDeJ8m839Q",
        "createdAt": 1753945570,
        "status": "OK",
        "trigger": "monitoring",
        "monitorId": "45VugCVZyZw",
        "targetValue": 31
    },
    {
        "id": "5wDea1kw9W5",
        "createdAt": 1753945269,
        "status": "WARNING",
        "trigger": "monitoring",
        "monitorId": "45VugCVZyZw",
        "targetValue": 52.4
    }
]
% mkr alerts logs 5wDea1jMahU --jq '.[].id'
5wDeJ8m839Q
5wDea1kw9W5
```

### カスタムダッシュボード

mkrではdashboardsサブコマンドでカスタムダッシュボードを操作できます。サブコマンドはpull/pushの2種類あります。サブコマンドを指定しない場合、カスタムダッシュボード一覧が表示されます。

- `pull`
  - Mackerelからカスタムダッシュボード一覧を取得し、複数の設定ファイルをカレントディレクトリに保存します。
- `push`
  - ローカルファイルのカスタムダッシュボード設定ファイルをMackerelに反映します。

カスタムダッシュボード設定ファイルのフォーマットについては、[API仕様の「ダッシュボードの作成」](https://mackerel.io/ja/api-docs/entry/dashboards#create)を参照してください。

#### push時のカスタムダッシュボードの同一性判定ロジック

`pull`する際はMackerelが付与した`id`が必ず含まれます。
`push`する場合は`id`が含まれないルールも許容するために以下のロジックで監視ルールの同一判定を行います。

- `id`を含む .. `id`で一致するカスタムダッシュボードをアップデート
  - 同じ`id`のダッシュボードがなければエラーとなります。
- `id`を含まない .. 新規のカスタムダッシュボードを作成

#### 実行例

- カスタムダッシュボード一覧を取得

リストの取得では、`widgets`は常に`null`となります。

```
% mkr dashboards
[
    {
        "id": "2LHfKUq36xW",
        "title": "demo",
        "urlPath": "2LHfFFsNvo1",
        "createdAt": 1470725548,
        "updatedAt": 1538371379,
        "memo": "",
        "widgets": null,
	},
    {
        "id": "49CBJiNaXfQ",
        "title": "demo2",
        "urlPath": "custom",
        "createdAt": 1470725548,
        "updatedAt": 1538371379,
        "memo": "",
        "widgets": null
	}
]
```

- カスタムダッシュボードをローカルファイルへ保存

```
% mkr dashboards pull
      info Dashboard file is saved to 'dashboard-2LHfKUq36xW.json'(title:demo)
      info Dashboard file is saved to 'dashboard-49CBJiNaXfQ.json'(title:demo2)
% ls
dashboard-2LHfKUq36xW.json	dashboard-49CBJiNaXfQ.json
```

- Mackerelのカスタムダッシュボードを更新

```
% mkr dashboards push --file-path dashboard-49CBJiNaXfQ.json
```

### グラフアノテーション
mkrでは、annotationsサブコマンドでグラフアノテーションに関連する操作を行なえます。サブコマンドはcreate/list/update/deleteの4種類あります。

各サブコマンドで返ってくるjsonフォーマットについては、[API仕様の「グラフアノテーション」](https://mackerel.io/ja/api-docs/entry/graph-annotations)を参照してください。

#### create
- グラフアノテーションを作成するサブコマンドです
- グラフアノテーションの作成に成功すると、作成したグラフアノテーションを表わすjsonが出力されます
- `service`、`from`、`to`、`title`が必須指定の指定パラメータです
- `description`や`role`(複数指定可能)を指定することもできます

```sh
% mkr annotations create --service My-Machine --from 1480125301 --to 1486125301 --title "Deploy" --description "Deployed"

{
    "id": "2VpN33ceumh",
    "title": "Deploy",
    "describe": "Deployed",
    "from": 1480125301,
    "to": 1486125301,
    "service": "My-Machine"
}
```

#### list
- 指定したサービス名(`service`)と期間内(`from`と`to`)にあるグラフアノテーションの一覧を返すサブコマンドです
- グラフアノテーションの一覧を表わすjsonが出力されます
- `service`、`from`、`to`、`title`が必須指定の指定パラメータです

```sh
% mkr annotations list --service My-Machine --from 1480125301 --to 1486125301

[
    {
        "id": "2VpN33ceumh",
        "title": "Deploy",
        "from": 1480125301,
        "to": 1486125301,
        "service": "My-Machine"
    }
]
```

#### update
- 指定したグラフアノテーションの更新を行なうサブコマンドです
- グラフアノテーションの更新に成功すると、更新されたグラフアノテーションを表わすjsonが出力されます
- `id`、`service`、`from`、`to`、`title`が必須指定の指定パラメータです
- `description`や`role`(複数指定可能)を指定することもできます

```sh
% mkr annotations update --id 2VpN33ceumh --service My-Machine --from 1485013461 --to 1485169804 --title "updated" --role Desktop --role Laptop

{
    "id": "2VpN33ceumh",
    "title": "updated",
    "from": 1485013461,
    "to": 1485169804,
    "service": "My-Machine",
    "roles": [
        "Desktop",
        "Laptop"
    ]
}
```

#### delete
- グラフアノテーションを削除するサブコマンドです
- グラフアノテーションの削除に成功すると、削除されたグラフアノテーションを表わすjsonが出力されます
- `id`が必須指定の指定パラメータです

```sh
% mkr annotations delete --id 2VpN33ceumh

{
    "id": "2VpN33ceumh",
    "title": "updated",
    "from": 1485013461,
    "to": 1485169804,
    "service": "My-Machine",
    "roles": [
        "Desktop",
        "Laptop"
    ]
}
```

### プラグイン
mkrでは、pluginサブコマンドでmackerelプラグインやcheckプラグインの管理を行えます。

#### install
Githubに置かれたプラグインや[プラグインレジストリ](https://github.com/mackerelio/plugin-registry)に登録されたプラグインをインストールできます。詳しくは[mkr plugin installでプラグインをインストールする](https://mackerel.io/ja/docs/entry/advanced/install-plugin-by-mkr)を参照してください。

### コマンドの実行結果の監視

mkrではwrapサブコマンドでコマンドの実行結果を監視できます。詳しくは下記のヘルプを参照してください。

[https://mackerel.io/ja/docs/entry/howto/mkr/wrap:embed:cite]
