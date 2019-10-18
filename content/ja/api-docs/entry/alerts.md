---
Title: アラート
Date: 2016-03-22T10:43:30+09:00
URL: https://mackerel.io/ja/api-docs/entry/alerts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368066225
---

<ul class="internal-nav">
  <li><a href="#get">アラートの取得</a></li>
  <li><a href="#close">アラートを閉じる</a></li>
</ul>

<h2 id="get">アラートの取得</h2>

アラートの一覧を取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alerts</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力（クエリパラメータ）

次のパラメータでアラートを絞り込むことができます。`withClosed`が指定されていない場合は、発生中のアラートのみが返却されます。
`nextId`が指定されていない場合は、直近のアラートの発生時刻の新しい順で返却されます。

| PARAM     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `withClosed` | *boolean* | [optional] 解決済みのアラートも取得するかどうか。真なら発生済みに加えて解決済みのアラートも取得されます。 |
| `nextId` | *string* | [optional] `nextId` が指定された場合は、指定されたアラートのidよりも以前のアラートを取得します。 |
| `limit` | *number* | [optional] 取得するアラートの上限数。省略時は100件まで取得します。指定できる最大は100です。 |


### 応答

#### 成功時

```json
{
  "alerts": [<alert>, <alert>, ...],
  "nextId": xxx
}
```

並び順は、アラートの発生時刻が新しい順です。 `nextId` はアラートが `limit` 件より多くあるときに取得されます。

<i>`<alert>`</i> は以下のキーを持つオブジェクトです。

| KEY      | TYPE            | DESCRIPTION                                       |
| -------- | ------          | -----------                                       |
| `id`     | *string*        | アラートのid。                                      |
| `status` | *string*        | アラートの現在のステータス `"OK"`、 `"CRITICAL"`、 `"WARNING"`、 `"UNKNOWN"` のいずれかになります。 |
| `monitorId`  | *string* | アラートを発生させた監視ルールのid。 |
| `type`  | *string* | 監視ルールの種別。疎通 (`"connectivity"`)、ホストメトリック (`"host"`)、サービスメトリック (`"service"`)、外形監視 (`"external"`)、チェック監視 (`"check"`)、式監視 (`"expression"`)、ロール内異常検知 (`"anomalyDetection"`) のいずれかになります。 |
| `hostId`  | *string* | [optional] 関連するホストのid。監視ルールの種別が `"connectivity"`、`"host"`、`"check"`、`"anomalyDetection"`の時のみ存在します。 |
| `value`  | *number* | [optional] 監視対象の値。監視ルールの種別が `"host"`、`"service"` の時または `"external"`で応答時間の設定があるときのみ存在します。 |
| `message`  | *string* | [optional] 監視対象のメッセージ。監視ルールの種別が `"check"` の時または `"external"` の時のみ存在します。 |
| `reason`  | *string* | [optional]  アラートを閉じた理由。アラートが解決していない時は存在しません。 |
| `openedAt`  | *number* | アラートの発生時刻 (epoch秒) |
| `closedAt`  | *number* | [optional]  アラートの解決時刻 (epoch秒)。アラートが既に解決している時のみ存在します。 |

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
      <td><code>limit</code> の値が最大値(100)より大きい場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="close">アラートを閉じる</h2>

指定したアラートを閉じます。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/close</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

```json
{
  "reason": <text>
}
```
`reason` フィールドには任意のテキストを記述できます。このフィールドは必須項目です。

### 応答

#### 成功時

更新後のアラートを返します。

```json
{
  "id": "<alertId>",
  "status": "OK",
  ...
  "reason": <reason>,
  "opendAt": <opendAt>,
  "closedAt": <closedAt>
}
```

[アラートの取得](#get)の項目の <i>`<alert>`</i> オブジェクトと同じです。 `reason` と `closedAt` が存在します。

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
      <td><code><em>&lt;alertId&gt;</em></code> に対応するアラートが見つからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
