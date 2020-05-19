---
Title: チェック監視
Date: 2016-03-22T10:37:32+09:00
URL: https://mackerel.io/ja/api-docs/entry/check-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368065674
---

<h2 id="post">チェック監視結果の投稿</h2>

チェック監視の結果を Mackerel に送信します。監視結果はホストに紐付きます。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/monitoring/checks/report</code>
</p>

[チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks) の実装に使われています。
エージェントは設定されたチェック監視の名前の一覧を [ホスト情報の更新](/ja/api-docs/entry/hosts#update-information) の API に定期的に送信しており、その時に一覧に含まれず、かつ発生中のアラートのないチェック監視は Mackerel から削除されます。
同じ名前/ホストで監視時刻が新しいものが既に投稿されていた場合、投稿は無視されます。

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

```json
{
  "reports": [ <report>, <report>, … ]
}
```

<i>`<report>`</i> : 以下のキーをもつオブジェクトです。1回のAPIリクエストでは最大100件の <i>`<report>`</i> を送信できます。

| KEY          | TYPE     | DESCRIPTION                      |
| ------------ | -------- | -------------------------------- |
| `source`     | *source* | 下記参照。                         |
| `name`       | *string* | 監視名。                           |
| `status`     | *string* | 監視結果のアラートステータス。`"OK"`、 `"CRITICAL"`、 `"WARNING"`、 `"UNKNOWN"` のいずれかです。 |
| `message`    | *string* | 監視結果のアラートステータスに付随する補助的なテキスト, 1024文字以下です。 |
| `occurredAt` | *number* | 監視実行時刻のエポック秒。 |
| `notificationInterval` | *number*   | [optional] アラートの再送間隔 (分) 省略した場合は再送されない。10分未満を指定した場合は、10分間隔で再送通知されます。 |
| `maxCheckAttempts` | *number* | [optional] 何回連続で Warning/Critical になったらアラートを発生させるか。デフォルトは1 (1~10)です。 |

<span class="table-code">source</span> : 以下のキーをもつオブジェクトです。

| KEY          | TYPE     | DESCRIPTION                      |
| ------------ | -------- | -------------------------------- |
| `type`       | *string* | 定数文字列 `"host"`              |
| `hostId`     | *string* | ホストID（ホスト登録時にサーバから与えられるもの）|

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
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>200以外</td>
      <td>上記以外のエラー</td>
    </tr>
  </tbody>
</table>
