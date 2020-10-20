---
Title: ユーザー
Date: 2016-03-22T10:48:29+09:00
URL: https://mackerel.io/ja/api-docs/entry/users
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368066650
---

<ul class="internal-nav">
  <li><a href="#list">オーガニゼーションメンバーに所属するユーザーの一覧</a></li>
  <li><a href="#delete">オーガニゼーションメンバーに所属するユーザーの削除</a></li>
</ul>

<h2 id="list">オーガニゼーションメンバーに所属するユーザーの一覧</h2>

オーガニゼーションにメンバーとして所属するユーザーの一覧を取得します。

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
| `joinedAt`                | *number*        | ユーザーがオーガニゼーションのメンバーに参加した日時 (epoch秒)                                                |

----------------------------------------------

<h2 id="delete">オーガニゼーションメンバーに所属するユーザーの削除</h2>

オーガニゼーションのメンバーに所属するユーザーを削除します。

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
オーガニゼーションのメンバーから削除されたユーザーの情報が返却されます。 形式は[オーガニゼーションメンバーに所属するユーザーの一覧](#list)で取得できるユーザー形式と同様です。

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
      <td>APIキーに書き込み権限がないとき / オーガニゼーション作成者を<code><em>&lt;userId&gt;</em></code>に指定したとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定された<code><em>&lt;userId&gt;</em></code>がオーガニゼーションのメンバーに所属していなかったとき</td>
    </tr>
  </tbody>
</table>
