---
Title: サービス
Date: 2016-03-18T17:42:15+09:00
URL: https://mackerel.io/ja/api-docs/entry/services
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792367516794
---

<ul class="internal-nav">
  <li><a href="#list">サービスの一覧</a></li>
  <li><a href="#create">サービスの登録</a></li>
  <li><a href="#delete">サービスの削除</a></li>
  <li><a href="#rolelist">ロールの一覧</a></li>
  <li><a href="#rolecreate">ロールの登録</a></li>
  <li><a href="#roledelete">ロールの削除</a></li>
  <li><a href="#metric-names">メトリック名の一覧</a></li>
</ul>

<h2 id="list">サービスの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "services": [<service>, <service>, ...]
}
```

<i>`<service>`</i> は以下のキーを持つオブジェクトです。

| KEY      | TYPE            | DESCRIPTION                              |
| -------- | ------          | -----------                              |
| `name`   | *string*        | サービス名。                                |
| `memo`   | *string*        | このサービスに関するメモ。Web UI から編集できます。 |
| `roles`  | *array[string]* | サービスに属するロール名の配列。                 |

配列の並び順はサービスの名前順です。

----------------------------------------------

<h2 id="create">サービスの登録</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/services</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY      | TYPE            | DESCRIPTION               |
| -------- | ------          | -----------               |
| `name`   | *string*        | サービス名。              |
| `memo`   | *string*        | このサービスに関するメモ。|

#### サービス名について

サービス名にはアルファベット(A-Za-z)、 数字(0-9)、ハイフン(-)、アンダースコア(_)が利用できます。
ただし、ハイフンとアンダースコアは先頭には使えず、2文字以上63文字以内に収める必要があります。

また、ひとつのオーガニゼーションの中に、同じ名前のサービスを複数つくることはできません。

#### 入力例

```json
{
  "name": "ExampleService",
  "memo": "This is an example."
}
```

### 応答
作成されたサービスが返却されます。フォーマットは<a href="#list">サービスの一覧</a>APIの<i>`<service>`</i>オブジェクトと同じ形式です。

#### 成功時

``` json
{
    "name": <serviceName>,
    "memo": <memo>,
    "roles": []
}
```

| KEY     | TYPE            | DESCRIPTION                          |
| ------- | --------------- | ------------------------------------ |
| `name`  | *string*        | サービス名。                         |
| `memo`  | *string*        | サービスに関するメモ。               |
| `roles` | *array[string]* | サービスに属するロール名の配列。作成直後は空になっています。 |

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
      <td><code><em>&lt;serviceName&gt;</em></code>が上記のサービス名の制約を満たさなかったとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">サービスの削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em></code>
</p>

サービスに紐付けられているロール、サービスメトリック、監視ルール、グラフアノテーションも同時に削除されます。

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答
削除される直前のサービスの状態が返却されます。

#### 成功時

``` json
{
    "name": <serviceName>,
    "memo": <memo>,
    "roles": [<roleName>, <roleName>, ...]
}
```

| KEY     | TYPE            | DESCRIPTION                          |
| ------- | --------------- | ------------------------------------ |
| `name`  | *string*        | サービス名。                         |
| `memo`  | *string*        | サービスに関するメモ。               |
| `roles` | *array[string]* | サービスに属していたロール名の配列。 |

配列の並び順はロールの名前順です。

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
      <td><code><em>&lt;serviceName&gt;</em></code>に対応するサービスが見つからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="rolelist">ロールの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "roles": [<role>, <role>, ...]
}
```

<i>`<role>`</i> は以下のキーを持つオブジェクトです。

| KEY      | TYPE     | DESCRIPTION                                     |
| -------- | ------   | -----------                                     |
| `name`   | *string* | ロール名。                                        |
| `memo`   | *string* | このロールに関するメモ。Web UI から編集できます。         |

配列の並び順はロールの名前順です。

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
      <td><code><em>&lt;serviceName&gt;</em></code>に対応するサービスが見つからないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="rolecreate">ロールの登録</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY      | TYPE            | DESCRIPTION               |
| -------- | ------          | -----------               |
| `name`   | *string*        | ロール名。              |
| `memo`   | *string*        | このロールに関するメモ。|

#### ロール名について

ロール名にはアルファベット(A-Za-z)、 数字(0-9)、ハイフン(-)、アンダースコア(_)が利用できます。
ただし、ハイフンとアンダースコアは先頭には使えず、2文字以上63文字以内に収める必要があります。

また、ひとつのサービスの中に、同じ名前のロールを複数つくることはできません。

#### 入力例

```json
{
  "name": "ExampleRole",
  "memo": "This is an example."
}
```

### 応答
作成されたロールが返却されます。フォーマットは<a href="#rolelist">ロールの一覧</a>APIの<i>`<role>`</i>オブジェクトと同じ形式です。

#### 成功時

``` json
{
    "name": <roleName>,
    "memo": <memo>
}
```

| KEY      | TYPE     | DESCRIPTION                                     |
| -------- | ------   | -----------                                     |
| `name`   | *string* | ロール名。                                        |
| `memo`   | *string* | このロールに関するメモ。Web UI から編集できます。         |


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
      <td><code><em>&lt;serviceName&gt;</em></code>に対応するサービスが見つからないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code><em>&lt;roleName&gt;</em></code>が上記のロール名の制約を満たさなかったとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="roledelete">ロールの削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答
削除される直前のロールの状態が返却されます。

#### 成功時

``` json
{
    "name": <roleName>,
    "memo": <memo>
}
```

| KEY      | TYPE     | DESCRIPTION                                     |
| -------- | ------   | -----------                                     |
| `name`   | *string* | ロール名。                                        |
| `memo`   | *string* | ロールに関するメモ。                               |

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
      <td><code><em>&lt;serviceName&gt;</em></code>に対応するサービスや<code><em>&lt;roleName&gt;</em></code>に対応するロールが見つからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="metric-names">メトリック名の一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metric-names</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "names": [<metricName>, <metricName>, ...]
}
```

| KEY      | TYPE            | DESCRIPTION                              |
| -------- | ------          | -----------                              |
| `names`  | *array[string]* | サービスに投稿されているメトリック名。         |

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
      <td><code><em>&lt;serviceName&gt;</em></code>に対応するサービスが見つからないとき</td>
    </tr>
  </tbody>
</table>
