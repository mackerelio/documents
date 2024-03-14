---
Title: アラート
Date: 2016-03-22T10:43:30+09:00
URL: https://mackerel.io/ja/api-docs/entry/alerts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368066225
---

<ul class="internal-nav">
  <li><a href="#list">アラートの一覧</a></li>
  <li><a href="#get">アラートの取得</a></li>
  <li><a href="#update">アラートの更新</a></li>
  <li><a href="#close">アラートを閉じる</a></li>
</ul>

<h2 id="list">アラートの一覧</h2>

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
| `type`  | *string* | 監視ルールの種別。疎通 (`"connectivity"`)、ホストメトリック (`"host"`)、サービスメトリック (`"service"`)、外形監視 (`"external"`)、チェック監視 (`"check"`)、式監視 (`"expression"`)、ロール内異常検知 (`"anomalyDetection"`)、クエリ監視(`"query"`) のいずれかになります。 |
| `hostId`  | *string* | [optional] 関連するホストのid。監視ルールの種別が `"connectivity"`、`"host"`、`"check"`、`"anomalyDetection"`の時のみ存在します。 |
| `series` | *object* | [optional] 監視対象の系列。監視ルールの種別が `"query"` の時のみ存在します。[*1](#series)|
| `value`  | *number* | [optional] 監視対象の値。監視ルールの種別が `"host"`、`"service"`、`"query"` の時または `"external"`で応答時間の設定がある時のみ存在します。 |
| `message`  | *string* | [optional] 監視対象のメッセージ。監視ルールの種別が `"check"` の時または `"external"` の時のみ存在します。 |
| `reason`  | *string* | [optional]  アラートを閉じた理由。アラートが解決していない時は存在しません。 |
| `openedAt`  | *number* | アラートの発生時刻 (epoch秒) |
| `closedAt`  | *number* | [optional]  アラートの解決時刻 (epoch秒)。アラートが既に解決している時のみ存在します。 |

<h4 id="series">*1 series</h4>

<span class="table-code">series</span>は以下のキーを持つオブジェクトです。

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | 監視対象のラベル対応メトリックの名前。 例: "container.cpu.utilization" |
| `labels` | *object* | 監視対象のラベル対応メトリックのラベル一覧。ラベルのキーと値をキーと値とするオブジェクト。例: `{"ip":"127.0.0.1"}` |


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

<h2 id="get">アラートの取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

アラートの情報が返却されます。形式は[アラートの一覧](#list)で取得できるアラートの形式と同様です。

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
      <td>指定されたIDのアラートがみつからないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">アラートの更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

```json
{
  "memo": "<text>"
}
```
`memo` フィールドには任意のテキストを記述できます。このフィールドは必須項目です。

### 応答

#### 成功時

アラートのIDとメモが返却されます。

```json
  "id": "<alertId>",
  "memo": "<text>"
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
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのアラートがみつからないとき</td>
    </tr>
    <tr>
      <td>413</td>
      <td>指定されたメモが80KBを超過している時</td>
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
  "reason": "<text>"
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
  "reason": "<reason>",
  "openedAt": <openedAt>,
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
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
