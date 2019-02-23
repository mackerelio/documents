---
Title: ダッシュボード（レガシー）
Date: 2018-11-14T15:13:25+09:00
URL: https://mackerel.io/ja/api-docs/entry/dashboards/legacy
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10257846132669669312
---

<ul class="internal-nav">
  <li><a href="#create">ダッシュボードの作成</a></li>
  <li><a href="#get">ダッシュボードの取得</a></li>
  <li><a href="#update">ダッシュボードの更新</a></li>
  <li><a href="#delete">ダッシュボードの削除</a></li>
  <li><a href="https://mackerel.io/ja/api-docs/entry/dashboards#list">ダッシュボードの一覧</a></li>
</ul>

このページでは2018年11月のリニューアル前のカスタムダッシュボード（レガシーカスタムダッシュボード）のAPIについて説明します。リニューアル後の新しいカスタムダッシュボードを操作するAPIについては[こちら](https://mackerel.io/ja/api-docs/entry/dashboards)をご覧ください。

<h2 id="create">ダッシュボードの作成</h2>

ダッシュボードを作成します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/dashboards</code>
</p>

すでに使われているurlPathを指定して新たにダッシュボードを作ることはできません。

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

以下のキーをもつオブジェクト。

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `title`   | *string* | ダッシュボード名。 |
| `bodyMarkdown`   | *string* | Markdown形式で書かれたダッシュボード詳細。 |
| `urlPath` | *string* | ダッシュボードのURLパス。 *1 |

*1 urlPathのフォーマットは以下の通りです：

`^([A-Za-z0-9_][-A-Za-z0-9_]*)(/[A-Za-z0-9_][-A-Za-z0-9_]*)*$`

### 応答

#### 成功時

作成されたダッシュボードを返します。

```json
{
  "id": <dashboardId>,
  "title": "My Dashboard",
  "bodyMarkdown": "# A test dashboard",
  "urlPath": "2u4PP3TJqbu",
  "createdAt": 1439346145003,
  "updatedAt": 1439346145003,
  "isLegacy": true
}
```

 <i>`<dashboardId>`</i> : このダッシュボードに割り当てられた ID です。以降のリクエストではこの ID を用いてダッシュボードを識別します。

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
      <td>403</td>
      <td>作成できるダッシュボード数の上限に達してるとき / APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>409</td>
      <td>すでに存在してるURLを指定してしまったとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">ダッシュボードの取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "id": <dashboardId>,
  "title": "My Dashboard",
  "bodyMarkdown": "# A test dashboard",
  "urlPath": "2u4PP3TJqbu",
  "createdAt": 1439346145003,
  "updatedAt": 1439346145003
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
      <td>400</td>
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのダッシュボードがみつからないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">ダッシュボードの更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

[ダッシュボードの作成](#create)と同様です。

### 応答

#### 成功時

更新後のダッシュボードを返します。[ダッシュボードの作成](#create) と同様の形式です。

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
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのダッシュボードがみつからないとき</td>
    </tr>
    <tr>
      <td>409</td>
      <td>すでに存在しているURLを指定してしまったとき *1 </td>
    </tr>
  </tbody>
</table>

 *1 ダッシュボードのURLも更新したい場合、既存のダッシュボードのURLと重複する可能性があるのでその際409が返されます。

----------------------------------------------

<h2 id="delete">ダッシュボードの削除</h2>

指定されたIDのダッシュボードを削除します。

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時

削除前のダッシュボードを返します。[ダッシュボードの作成](#create) と同様です。

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
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのダッシュボードがみつからないとき</td>
    </tr>
  </tbody>
</table>
