---
Title: 招待
Date: 2017-04-18T12:26:25+09:00
URL: https://mackerel.io/ja/api-docs/entry/invitations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328749687237775981
---

<ul class="internal-nav">
  <li><a href="#list">招待の一覧</a></li>
  <li><a href="#create">招待の作成</a></li>
  <li><a href="#revoke">招待の取り消し</a></li>
</ul>

<h2 id="list">招待の一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/invitations</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "invitations": [<invitation>, <invitation>, ...]
}
```

<i>`<invitation>`</i> は以下のキーを持つオブジェクトです。

| KEY         | TYPE     | DESCRIPTION                                                 |
| --------    | ------   | -----------                                                 |
| `email`     | *string* | 招待を送ったメールアドレス。                                |
| `authority` | *string* | 招待から加入した時の権限(`manager`,`collaborator`,`viewer`) |
| `expiresAt` | *number* | 招待の有効期限 (epoch秒)                                    |


<h2 id="create">招待の作成</h2>

メールアドレスと権限を指定してユーザーをオーガニゼーションに招待します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/invitations</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY             | TYPE            | DESCRIPTION                                                    |
| ------------    | --------------- | -------------------------------------------------------------- |
| `email`         | *string*          | 招待を送るメールアドレス                                       |
| `authority`     | *string*          | 招待から加入したときの権限 (`manager`,`collaborator`,`viewer`) |

#### 入力例
```json
{
  "email": "example@example.com",
  "authority": "viewer"
}
```

### 応答

#### 成功時

入力にexpiresAt(epoch秒)が付与されて返却されます。招待はexpiresAtの時刻以降は使えなくなります。

```json
{
  "email": "example@example.com",
  "authority": "viewer",
  "expiresAt": 1492393387
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
      <td>JSON のフォーマットが不正であるとき、既に招待しているメールアドレスやメンバーのメールアドレスが指定されたとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="revoke">招待の取り消し</h2>

メールアドレスを指定してオーガニゼーションへの招待を取り消します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/invitations/revoke</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY             | TYPE            | DESCRIPTION                                                    |
| ------------    | --------------- | -------------------------------------------------------------- |
| `email`         | *string*        | 招待を取り消すメールアドレス                                   |

#### 入力例

```json
{
  "email": "example@example.com"
}
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
      <td>400</td>
      <td>JSON のフォーマットが不正であるとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定したemailの招待がなかった時</td>
    </tr>
  </tbody>
</table>
