---
Title: 通知グループ
Date: 2017-10-02T16:07:18+09:00
URL: https://mackerel.io/ja/api-docs/entry/notification-groups
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/8599973812303795755
---

<ul class="internal-nav">
  <li><a href="#create">通知グループの登録</a></li>
  <li><a href="#get">通知グループの一覧取得</a></li>
  <li><a href="#update">通知グループの更新</a></li>
  <li><a href="#delete">通知グループの削除</a></li>
</ul>

<h2 id="create">通知グループの登録</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/notification-groups</code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY                         | TYPE             | DESCRIPTION                              |
| --------------------------- | ---------------- | ---------------------------------------- |
| `name`                      | *string*         | 通知グループの名称                       |
| `notificationLevel`         | *string*         | 通知レベル (`"all"` または `"critical"`) |
| `childNotificationGroupIds` | *array[string]*  | 通知先の通知グループのIDの配列           |
| `childChannelIds`           | *array[string]*  | 通知先の通知チャンネルのIDの配列         |
| `monitors`                  | *array[monitor]* | [optional] 通知対象の監視ルールの配列    |
| `services`                  | *array[service]* | [optional] 通知対象のサービスの配列      |

`monitor` は以下のキーを持つオブジェクトです。

| KEY                         | TYPE             | DESCRIPTION                                    |
| --------------------------- | ---------------- | ---------------------------------------------- |
| `id`                        | *string*         | 監視ルールのID                                 |
| `skipDefault`               | *boolean*        | trueの場合、指定の通知グループにのみに通知する |

`service` は以下のキーを持つオブジェクトです。

| KEY                         | TYPE             | DESCRIPTION                              |
| --------------------------- | ---------------- | ---------------------------------------- |
| `name`                      | *string*         | サービスの名称                           |

### 入力例

```json
{
  "name": "Example notification group",
  "notificationLevel": "all",
  "childNotificationGroupIds": [],
  "childChannelIds": [
    "2vh7AZ21abc"
  ],
  "monitors": [
    {
      "id": "2qtozU21abc",
      "skipDefault": false
    }
  ],
  "services": [
    {
      "name": "Example-Service-1"
    },
    {
      "name": "Example-Service-2"
    }
  ]
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
      <td>作成に失敗したとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="get">通知グループの一覧取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/notification-groups</code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "notificationGroups": [<notification-group>, <notification-group>, ...]
}
```

`<notification-group>` は登録APIの応答と同じ形です。

<h2 id="update">通知グループの更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

登録APIの入力と同じオブジェクトです。

### 応答

登録APIの応答と同じオブジェクトです。

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
      <td>デフォルト通知グループの名前を変更しようとしたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>更新に失敗したとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定された通知グループが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="delete">通知グループの削除</h2>
オーガニゼーションのデフォルト通知グループは削除できません。

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

削除前の通知グループが返却されます。

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
      <td>指定された通知グループがデフォルト通知グループのとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>削除に失敗したとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定された通知グループが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
