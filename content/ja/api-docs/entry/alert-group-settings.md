---
Title: アラートグループ設定
Date: 2019-09-03T16:29:55+09:00
URL: https://mackerel.io/ja/api-docs/entry/alert-group-settings
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/26006613418424326
---

<ul class="internal-nav">
  <li><a href="#list">アラートグループ設定の一覧</a></li>
  <li><a href="#create">アラートグループ設定の作成</a></li>
  <li><a href="#get">アラートグループ設定の取得</a></li>
  <li><a href="#update">アラートグループ設定の更新</a></li>
  <li><a href="#delete">アラートグループ設定の削除</a></li>
</ul>

<h2 id="list">アラートグループ設定の一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alert-group-settings</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "alert_group_settings": [<alertGroupSetting>, <alertGroupSetting>, ...]
}
```

<i>`<alertGroupSetting>`</i> は以下のキーを持つオブジェクトです。

| KEY                    | TYPE            | DESCRIPTION                                                            |
| --------               | ------          | -----------                                                            |
| `id`                   | *string*        | アラートグループ設定ID                                                 |
| `name`                 | *string*        | アラートグループ設定の名称                                             |
| `memo`                 | *string*        | [optional] このアラートグループ設定に関するメモ                        |
| `serviceScopes`        | *array[string]* | [optional] サービス対象スコープ。サービス名の配列                      |
| `roleScopes`           | *array[string]* | [optional] ロール対象スコープ。ロール詳細名の配列 [*1](#role-fullname) |
| `monitorScopes`        | *array[string]* | [optional] 監視ルール対象スコープ。監視ルールIDの配列                      |
| `notificationInterval` | *number*        | [optional] 通知の再送設定をするときの再送間隔 (分)                     |

<h4 id="role-fullname">*1 ロール詳細名</h4>

ロールの詳細な名前は `<service name>:<role name>` というフォーマットの文字列です。

<table class="eg-table">
  <tbody>
  <tr>
    <th>e.g.</th>
    <td>Hatena-Bookmark サービスの db-master ロールなら<code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

使用できる文字列は `/^[A-Za-z0-9][A-Za-z0-9_-]+$/`。

<h2 id="create">アラートグループ設定の作成</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/alert-group-settings</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY                    | TYPE            | DESCRIPTION                                                            |
| --------               | ------          | -----------                                                            |
| `name`                 | *string*        | アラートグループ設定の名称                                             |
| `memo`                 | *string*        | [optional] このアラートグループ設定に関するメモ                        |
| `serviceScopes`        | *array[string]* | [optional] サービス対象スコープ。サービス名の配列                      |
| `roleScopes`           | *array[string]* | [optional] ロール対象スコープ。ロール詳細名の配列 [*1](#role-fullname) |
| `monitorScopes`        | *array[string]* | [optional] 監視ルール対象スコープ。監視ルールIDの配列                      |
| `notificationInterval` | *number*        | [optional] 通知の再送設定をするときの再送間隔 (分)                     |

### 応答

#### 成功時

作成されたアラートグループ設定の情報が返却されます。形式は[アラートグループ設定の一覧](#list)で取得できるアラートグループ設定の形式と同様です。

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
      <td>入力が不正であるとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="get">アラートグループ設定の取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

アラートグループ設定の情報が返却されます。形式は[アラートグループ設定の一覧](#list)で取得できるアラートグループ設定の形式と同様です。

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
      <td>入力が不正であるとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのアラートグループ設定がみつからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="update">アラートグループ設定の更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

[アラートグループ設定の作成](#create)と同様の形式です。

### 応答

#### 成功時

更新されたアラートグループ設定の情報が返却されます。形式は[アラートグループ設定の一覧](#list)で取得できるアラートグループ設定の形式と同様です。

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
      <td>入力が不正であるとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのアラートグループ設定がみつからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="delete">アラートグループ設定の削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時

削除されたアラートグループ設定の情報が返却されます。形式は[アラートグループ設定の一覧](#list)で取得できるアラートグループ設定の形式と同様です。

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
      <td>入力が不正であるとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのアラートグループ設定がみつからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
