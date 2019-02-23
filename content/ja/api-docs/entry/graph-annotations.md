---
Title: グラフアノテーション
Date: 2017-01-24T16:49:20+09:00
URL: https://mackerel.io/ja/api-docs/entry/graph-annotations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328749687209536556
---

<ul class="internal-nav">
  <li><a href="#create">グラフアノテーションの作成</a></li>
  <li><a href="#get">グラフアノテーションの取得</a></li>
  <li><a href="#update">グラフアノテーションの更新</a></li>
  <li><a href="#delete">グラフアノテーションの削除</a></li>
</ul>

<h2 id="create">グラフアノテーションの作成</h2>
<p class="type-post">
  <code>POST</code>
  <code>/api/v0/graph-annotations</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY             | TYPE            | DESCRIPTION                        |
| ------------    | --------------- | ---------------------------------- |
| `title`         | *string*        | アノテーションのタイトル           |
| `description`   | *string*        | [optional] アノテーションの詳細    |
| `from`          | *number*        | 開始時刻 (epoch秒)                 |
| `to`            | *number*        | 終了時刻 (epoch秒)                 |
| `service`       | *string*        | サービス名                         |
| `roles`         | *array[string]* | [optional] ロール名の配列。このフィールドを指定しない場合は、サービスに紐づくアノテーションとなる |

#### 入力例
```json
{
  "title": "deploy application",
  "description": "link: https://example.com/",
  "from": 1484000000,
  "to": 1484000030,
  "service": "ExampleService",
  "roles": [ "ExampleRole1", "ExampleRole2" ]
}
```

### 応答
入力にidが付与されて返却されます。

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
      <td>400</td>
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>titleが250文字もしくはdescriptionが1024文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>開始時刻が終了時刻を超えているとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>サービスやロールが存在しないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="get">グラフアノテーションの取得</h2>

サービスと期間を指定して、グラフのアノテーションの一覧を取得します。
指定された期間と共通部分があるアノテーションが全て返却されます。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/graph-annotations</code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

### 入力 (クエリパラメータ)

| PARAM     | TYPE     | DESCRIPTION                                  |
| --------- | -------- | -------------------------------------------- |
| `service` | *string* | サービス名                                   |
| `from`    | *number* | 取得するアノテーションの期間の最初 (epoch秒) |
| `to`      | *number* | 取得するアノテーションの期間の最後 (epoch秒) |

### 応答

```json
{
  "graphAnnotations": [
    {
      "id": "2UdH1QcZQaw",
      "title": "Deploy application",
      "description": "Deploy description",
      "from": 1484020459,
      "to": 1484020759,
      "service": "ExampleService"
    },
    {
      "id": "2UdH1QuiGgj",
      "title": "Release application",
      "description": "Release description",
      "from": 1484021239,
      "to": 1484021239,
      "service": "ExampleService",
      "roles": [ "ExampleRole1", "ExampleRole2" ]
    }
  ]
}
```

### 失敗時

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>必要なクエリパラメータが指定されていないとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたサービスが存在しない時</td>
    </tr>
  </tbody>
</table>

<h2 id="update">グラフアノテーションの更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

[グラフアノテーションの作成](#create)と同様です。

### 応答

[グラフアノテーションの作成](#create)と同様、idが付与されて返却されます。

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
      <td>400</td>
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>titleが250文字もしくはdescriptionが1024文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>開始時刻が終了時刻を超えているとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>サービスやロールが存在しないとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>グラフのアノテーションが存在しないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="delete">グラフアノテーションの削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時

削除前のグラフのアノテーションが返却されます。

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
      <td>グラフのアノテーションが存在しないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
