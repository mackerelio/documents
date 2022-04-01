---
Title: ホストメトリック
Date: 2016-03-18T19:19:24+09:00
URL: https://mackerel.io/ja/api-docs/entry/host-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792367525698
---

 <ul class="internal-nav">
  <li><a href="#post">メトリックの投稿</a></li>
  <li><a href="#get">ホストのメトリックの値の取得</a></li>
  <li><a href="#get-latest">メトリックの最新値の取得</a></li>
  <li><a href="#post-graphdef">グラフ定義の投稿</a></li>
</ul>

<h2 id="post">メトリックの投稿</h2>

エージェントが収集するメトリック（計測値）を Mackerel に送信します。記録された値は Web のグラフなどで確認できます。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/tsdb</code>
</p>

APIに対して過去の値を送信した場合、Mackerel上の値は上書きされます。24時間以上前の古いタイムスタンプで投稿した場合は、そのメトリックは記録されません（ステータスコード 200 でレスポンスを返します）。また、アラートの発生はMackerel上の最新の値に基づいて行われるため、そのデータが最新の値ではない場合には、閾値を超えていたとしてもアラートは発生しません。

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

```json
[ <metricValue>, <metricValue>, … ]
```

<i>`<metricvalue>`</i> : 以下のキーをもつオブジェクトです。

| KEY      | TYPE     | DESCRIPTION |
| -------- | -------- | ----------- |
| `hostId` | *string* | ホスト ID（ホスト登録時にサーバから与えられるものです） |
| `name`   | *string* | メトリック名（`[a-zA-Z0-9._-]+`） |
| `time`   | *number* | エポック秒。 |
| `value`  | *number* | `time` 時点での計測値。 |

### 応答

#### 成功時

```json
{
  "sucess": true
}
```

#### 失敗時

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>200以外</td>
      <td>上記以外のエラー</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">ホストのメトリックの値の取得</h2>

エージェントから投稿されたメトリックの値を取得できます。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metrics</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力（クエリパラメータ）

次のパラメータでメトリック名と取得する期間を指定します。

| KEY     | TYPE     | DESCRIPTION |
| ------- | -------- | ----------- |
| `name`  | *string* | メトリック名（`loadavg5` など。詳しくは → [メトリック仕様](https://mackerel.io/ja/docs/entry/spec/metrics)） |
| `from`  | *number* | メトリックを取得したい期間の最初 (epoch秒) |
| `to`    | *number* | メトリックを取得したい期間の最後 (epoch秒) |

### 応答

#### 成功時

```json
{
  "metrics": [
    {
      "time": <time>,
      "value": <value>
    },
    {
      "time": <time>,
      "value": <value>
    },
    …
  ]
}
```

- 並び順は、時刻が古い順です
- メトリックの時刻を表す <i>`<time>`</i> はエポック秒です

#### 失敗時

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>404</td>
      <td>ホストまたはメトリックが存在しない時</td>
    </tr>
    <tr>
      <td>200以外</td>
      <td>上記以外のエラー</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get-latest">メトリックの最新値の取得</h2>

エージェントから投稿されたメトリックの最新の値をホストごとに取得できます。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/tsdb/latest</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力（クエリパラメータ）

次のパラメータでホストとメトリック名を指定します。

| KEY      | TYPE     | DESCRIPTION |
| -------- | -------- | ----------- |
| `hostId` | *string* | ホストID、複数指定可能。 |
| `name`   | *string* | メトリック名（`loadavg5` など。詳しくは → [メトリック仕様](https://mackerel.io/ja/docs/entry/spec/metrics)）、複数指定可能。 |

### 応答

#### 成功時

```json
{
  "tsdbLatest": {
    <hostId>: {
      <name>: <metricValue>,
      <name>: <metricValue>,
      …
    },
    <hostId>: {
      …
    },
    …
  }
}
```

<i>`<metricvalue>`</i> : 以下のキーをもつオブジェクトです。

| KEY      | TYPE     | DESCRIPTION |
| -------- | -------- | ----------- |
| `time`   | *number* | エポック秒。 |
| `value`  | *number* | `time` 時点での計測値。 |

#### 失敗時

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>200以外</td>
      <td>上記以外のエラー</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="post-graphdef">グラフ定義の投稿</h2>

カスタムメトリックのグラフ定義を Mackerel に送信します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/graph-defs/create</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

```json
[ <graphDef>, <graphDef>, … ]
```

<i>`<graphDef>`</i> : 以下のキーをもつオブジェクトです。

| KEY           | TYPE     | DESCRIPTION                                    |
| --------------| -------- | ---------------------------------------------- |
| `name`        | *string* | メトリック名の最後の `.` より前の部分。 `custom.` ではじまる必要があります。また、ワイルドカード`#`, `*` を使用することもできます。 |
| `displayName` | *string* | [optional] グラフの表示名。                                |
| `unit`        | *string* | [optional] グラフの値の種類。可能な値は "float", "integer", "percentage", "bytes", "bytes/sec", "iops" のいずれかです。 |
| `metrics`     | *array*  | `[ <metric>, <metric>, ...]` metric は下記参照。 |

<i>`<metric>`</i> : 以下のキーをもつオブジェクト。</p>

| KEY           | TYPE      | DESCRIPTION              |
| ------------- | --------- | ------------------------ |
| `name`        | *string*  | カスタムメトリック名。 `<graphDef>.name` の後ろに `.` に続けて名前（`[-a-zA-Z0-9_]+`またはワイルドカード`*`, `#`）を繋げたものである必要があります。ワイルドカードを使用する場合、`<graphDef>.name` にもワイルドカードの指定が必要になります。 |
| `displayName` | *string*  | [optional] メトリックの表示名。 省略した場合はメトリック名の最後の`.` の後ろが使用されます。`%1`、`%2`.. のようにしてメトリック名の1つ目、2つ目.. のワイルドカードにマッチした部分を利用できます。|
| `isStacked`   | *boolean* | 対応するメトリックを積み上げ表示するかどうか。偽なら折れ線で表示されます。 |

#### 入力例

```json
[
  {
      "name" : "custom.cpu.foo",
      "displayName": "CPU",
      "unit":"percentage",
      "metrics": [
         { "name": "custom.cpu.foo.user", "displayName": "CPU user", "isStacked": true },
         { "name": "custom.cpu.foo.idle", "displayName": "CPU idle", "isStacked": true },
         ...
      ]
  },
  {
      "name" : "custom.wild.#",
      "displayName": "wildcard",
      "unit":"float",
      "metrics": [
         { "name": "custom.wild.#.foo", "displayName": "wild foo" },
         { "name": "custom.wild.#.bar", "displayName": "wild bar" },
         ...
      ]
  },
  {
    "name": "custom.cpu.bar",
    ...
  }
]
```


### 応答

#### 成功時

```json
{
  "success": true
}
```

#### 失敗時

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>200以外</td>
      <td>上記以外のエラー</td>
    </tr>
  </tbody>
</table>

<h3 class="annotation">カスタムメトリック名とワイルドカードについて</h3>

- カスタムメトリック名に使用できる文字は英数字もしくはハイフン（-）、アンダースコア（_）のいずれか、または連続しない ドット（.）です。
- カスタムメトリック名の先頭は `custom.` とする必要があります。（mackerel-agent は `custom.` を自動で付与します。）
- またグラフ定義のメトリック名にはワイルドカード（`*` または `#`）を ２つのドット（.）の間、または最後のドット（.）の後ろに単独で使用できます。
- ワイルドカードはドットを除く文字の連続 `[-a-zA-Z0-9_]+` にマッチします。
- また、凡例のグループ化（後述）が可能なワイルドカード `#` は一つまでしか使えません。メトリック名全体は `^custom(\.([-a-zA-Z0-9_]+|[*#]))+$` のようになります。

ワイルドカード `#` を使った場合はメトリック名の`#` にマッチした部分でグラフの凡例がグループ化されます。
例えば `custom.docker.cpu.#.user`、 `custom.docker.cpu.#.system` という２つの定義があって

- `custom.docker.cpu.f5240a.user`
- `custom.docker.cpu.f5240a.system`
- `custom.docker.cpu.e866aq.user`
- `custom.docker.cpu.e866aq.system`
- `custom.docker.cpu.e552ad.user`
- `custom.docker.cpu.e552ad.system`

のように6つのカスタムメトリックを送信した場合の凡例は下のようになります。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150909/20150909172654.png" alt="f:id:mackerelio:20150909172654p:plain" title="f:id:mackerelio:20150909172654p:plain" class="hatena-fotolife" itemprop="image"></span></p>

また、ワイルドカードを含むグラフ定義のカスタムメトリックは一定時間（およそ6〜8時間以上）送信がない場合自動的に削除されますのでご注意ください。
