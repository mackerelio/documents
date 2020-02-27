---
Title: ユーザー
Date: 2016-03-22T10:48:29+09:00
URL: https://mackerel.io/ja/api-docs/entry/users
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368066650
---

<ul class="internal-nav">
  <li><a href="#list">ユーザーの一覧</a></li>
  <li><a href="#delete">ユーザーの削除</a></li>
</ul>

<h2 id="list">ユーザーの一覧</h2>

オーガニゼーションに所属するユーザーの一覧を取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/users</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "users": [<user>, <user>, ...]
}
```

<i>`<user>`</i> は以下のキーを持つオブジェクト

| KEY                       | TYPE            | DESCRIPTION                                                                                         |
| --------------            | --------        | -----------                                                                                         |
| `id`                      | *string*        | ユーザーのid。                                                                                      |
| `screenName`              | *string*        | ユーザーアカウントの表示名。                                                                        |
| `email`                   | *string*        | ユーザーのメールアドレス。                                                                          |
| `authority`               | *string*        | ユーザーの権限(`owner`, `manager`, `collaborator`, `viewer`)。                                      |
| `isInRegistrationProcess` | *boolean*       | ユーザーが仮登録かどうか。                                                                          |
| `isMFAEnabled`            | *boolean*       | ユーザーの2段階認証が有効かどうか。                                                                 |
| `authenticationMethods`   | *array[string]* | ユーザーが利用している認証方式(`password`, `github`, `idcf`, `google`, `nifty`, `yammer`, `kddi`)。 |
| `joinedAt`                | *number*        | ユーザーがオーガニゼーションに参加した日時 (epoch秒)                                                |

----------------------------------------------

<h2 id="delete">ユーザーの削除</h2>

オーガニゼーションに所属するユーザーを削除します。

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/users/<em>&lt;userId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時
オーガニゼーションから削除されたユーザーの情報が返却されます。 形式は[ユーザー一覧](#list)で取得できるユーザー形式と同様です。

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
      <td>APIキーに書き込み権限がないとき / オーガニゼーション作成者を<code><em>&lt;userId&gt;</em></code>に指定したとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定された<code><em>&lt;userId&gt;</em></code>がオーガニゼーションに所属していなかったとき</td>
    </tr>
  </tbody>
</table>
