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