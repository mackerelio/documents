---
Title: CLIツール mkr を使う
Date: 2015-06-19T12:03:19+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/cli
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450097006151
---

CLIツールmkrを利用することで、ホストのステータスをまとめて変更するような操作や、手順をスクリプトに組込み自動化することができるようになります。

mkrはGitHubで公開しております。
https://github.com/mackerelio/mkr

## 使う前に

### インストール

yumやaptを利用の場合は、mackerel-agentでも使われているMackerelのpackageリポジトリが追加されている必要があります。

- Amazon Linux: [Amazon Linuxにmackerel-agentをインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/amazon-linux)
- Redhat系OS・RPMパッケージ (yum): [CentOS / RedHat にインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/rpm).
- Debian系OS・debパッケージ (apt): [Ubuntu / Debian にインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/deb)

#### yumを利用

```cdl
% yum install mkr
```

#### aptを利用

```cdl
% apt-get install mkr
```

#### brewを利用

```cdl
% brew tap mackerelio/mackerel-agent
% brew install mkr
```

#### goでビルド

```cdl
% go get github.com/mackerelio/mkr
```

### セットアップ

mkrを使うためには、まずAPIキーを環境変数で指定します。
```cdl
export MACKEREL_APIKEY=<API key>
```

## 使い方

詳しい使い方は https://github.com/mackerelio/mkr をご参照ください。

### ホスト関連

例えばhostIdを指定して、そのホストの情報を取得できます。
```cdl
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

```cdl
mkr update --status maintenance <hostIds>...
```

ホスト一覧を取得することが可能です。
```cdl
mkr hosts --service My-Service --role proxy
```

これらを組み合わせて、指定したサービスとロールのホストのステータスをまとめて変更できます。

```cdl
mkr update --st working $(mkr hosts -s My-Service -r proxy | jq -r '.[].id')
```

### メトリック取得

mkrでは metrics サブコマンドでメトリックを取得できます。
例えば hostId を指定することで特定のホストのメトリックを、あるいはサービス名を指定することで特定のサービスのメトリックを取得できます。

```cdl
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

### 監視ルール

mkrではmonitorsサブコマンドで監視ルールを操作できます。サブコマンドは、pull/diff/pushの3種類あります。サブコマンドを指定しない場合、監視ルール一覧が表示されます。

- `pull`
  - Mackerelから監視ルール一覧を取得し、ローカルファイルに保存します。
- `diff`
  - Mackerelに設定されている監視ルール一覧と、ローカルファイルとの差分を表示します。
- `push`
  - ローカルファイルの監視ルールの設定をMackerelに反映します。

jsonフォーマットについては、[API仕様の「監視設定の登録」](https://mackerel.io/ja/api-docs/entry/monitors#create)を参照してください

#### push時の監視ルールの同一性判定ロジック

`pull`する際はMackerelが付与した`id`が必ず含まれます。
`push`する場合は`id`が含まれないルールも許容するために以下のロジックで監視ルールの同一判定を行います。

- `id`を含む .. `id`で同一判定
- `id`を含まず`name`を含む .. `name`で同一判定
  - 同じ`name`を持つ監視ルールが存在した場合、`id`でのみ同一判定を行います。そのため全ての監視ルールに`id`が含まれる必要があります。またWarningメッセージを出力します。
- `id`も`name`も含まないルールがある場合はエラーとなります。

#### 実行例

- ローカルファイルとMackerel間の差分がない場合の`diff`
```cdl
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

### ダッシュボードを生成する

mkrではdashboardsサブコマンドでカスタムダッシュボードを定義ファイルから自動生成できます。

定義ファイルはYAML形式です。

#### 実行例

以下のように定義ファイルをパラメータで指定して実行することで、mkrに設定されているAPIキーに対応するオーガニゼーション内にカスタムダッシュボードが自動的に作成されます。
```cdl
% mkr dashboards generate config.yml
```

また、`--print`オプションを指定することで、Mackerelへの登録・更新を行わずに標準出力にダッシュボードのmarkdownを出力することもできます。
```cdl
% mkr dashboards generate config.yml --print
```

#### 定義ファイル(YAML)の仕様

mkrで生成できるダッシュボードは2種類あります。

1. ホストとメトリックを指定するホストグラフの一覧
2. サービスメトリックや式によるグラフなどを個別に指定するグラフの一覧

それぞれのフォーマット例は以下のようになります。

##### ホストグラフ
```yaml
config_version: 0.9
url_path: custom-graph
title: Custom Host Graphs
format: iframe
height: 200
width: 400
host_graphs:
  - headline: host graphs 1
    host_ids:
     - XXXXXXXXXXX
     - YYYYYYYYYYY
     - ZZZZZZZZZZZ
    graph_names:
     - loadavg5
     - cpu
     - filesystem
     - custom.linux.disk.elapsed.*
    period: 30m
  - headline: host graphs 2
    host_ids:
     - XXXXXXXXXXX
    graph_names:
     - loadavg5
     - memory
    period: 30m
```

##### 個別グラフ指定
```yaml
config_version: 0.9
url_path: custom-graph
title: Custom Graphs
format: iframe
graphs:
  - headline: any graphs
    column_count: 2
    graph_def:
     - service_name: sugoi-service
       graph_name: custom.access_latency.*
     - service_name: sugoi-service
       role_name: api
       graph_name: loadavg5
       stacked: false
       simplified: true
       period: 6h
     - host_id: XXXXXXXXX
       graph_name: custom.inode.count.#.*
       period: 30m
     - host_id: XXXXXXXXX
       graph_name: filesystem
       period: 30m
  - headline: expression graph
    graph_def:
     - query: avg(roleSlots('sugoi-service:api',sum(group(host($HOST_ID, 'cpu.system.percentage'), host($HOST_ID, 'cpu.user.percentage')))))
       period: 30m
       title: graph title
       unit: float
```

#### YAMLの各項目

##### 共通
| 項目名          | type | 説明  |
|--------------|--|--------------------------------------|
| config_version | string |yamlの定義ファイルのversion。 現在は0.9固定 |
| url_path       | string | カスタムダッシュボードのURL。上記例だと`https://mackerel.io/orgs/<orgname>/dashboards/custom-graph`となる |
| title          | string | カスタムダッシュボードのタイトル |
| format         | string | [optional]グラフの形式。`iframe` or `image`。デフォルト値は`iframe` 。|
| height         | int | [optional]グラフの高さ。formatが`iframe`の場合のみ有効。デフォルト値は200。|
| width          | int | [optional]グラフの幅。formatが`iframe`の場合のみ有効。デフォルト値は400。|
| host_graphs    | array[host_graphs] | 以下にホストグラフの定義が配列形式で続く。`graphs` といずれか片方のみ指定できる。 |
| graphs         | array[graphs] | 以下に個別グラフ指定の定義が配列形式で続く。`host_graphs` といずれか片方のみ指定できる。 |

##### ホストグラフの定義
上記の `host_graphs`配下の項目として配列形式で定義。

| 項目名          | type | 説明  |
|--------------|--|--------------------------------------|
| headline       | string | 表の見出し |
| host_ids       | array[string] | 一覧に展開させるホストの一覧。配列形式で指定。|
| graph_names    | array[string] | 一覧に展開させるグラフ名の一覧。配列形式で指定。|
| period         | string | [optional]グラフの表示期間。デフォルト値は"1h"。使用可能な単位は`m`, `h`, `d`, `w`, `mo`, `y`。|


##### 個別グラフの定義
上記の `graphs`配下の項目として配列形式で定義。
定義方法により、以下のグラフ形式に対応。

- `service_name` のみの指定でサービスグラフ
- `service_name`, `role_name` の指定でロールグラフ
- `host_id` の指定でホストグラフ
- `query` の指定で式によるグラフ
- サービスグラフ, ロールグラフ, ホストグラフ, 式によるグラフのいずれにも該当しなければエラー

| 項目名          | type | 説明  |
|--------------  |------|--------------------------------------|
| headline       | string | 表の見出し |
| column_count   | int | [optional]生成されるグラフのカラム数。デフォルト値は1。 |
| graph_def      | array[graph_def] | 以下にそれぞれのグラフの定義を配列形式で指定 |
| service_name   | string | サービス名 |
| role_name      | string | ロール名   |
| host_id        | string | ホストID |
| query          | string | 式 |
| title          | string | [optional]式グラフ（query）を指定している場合のみ指定可能。式グラフのタイトルを指定 |
| unit           | string | [optional]式グラフ（query）を指定している場合のみ指定可能。式グラフのメトリックの単位を指定 |
| graph_name     | string | グラフ名 |
| period         | string | [optional]グラフの表示期間。デフォルト値は"1h"。使用可能な単位は`m`, `h`, `d`, `w`, `mo`, `y`。|
| stacked        | boolean | [optional]積み上げグラフかどうか。デフォルト値はfalse。ロールグラフのみ有効 |
| simplified     | boolean | [optional]簡略表示かどうか。デフォルト値はfalse。ロールグラフのみ有効 |

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
