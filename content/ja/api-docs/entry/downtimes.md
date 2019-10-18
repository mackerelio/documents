---
Title: ダウンタイム
Date: 2019-07-08T13:00:00+09:00
URL: https://mackerel.io/ja/api-docs/entry/downtimes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/26006613426005209
---

<ul class="internal-nav">
  <li><a href="#create">ダウンタイムの登録</a></li>
  <li><a href="#list">ダウンタイムの一覧</a></li>
  <li><a href="#update">ダウンタイムの更新</a></li>
  <li><a href="#delete">ダウンタイムの削除</a></li>
</ul>

<h2 id="create">ダウンタイムの登録</h2>

ダウンタイムの設定をMackerelに登録します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/downtimes</code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力
以下のキーをもつオブジェクト。

| KEY | TYPE | DESCRIPTION |
| --- | ---- | ----------- |
| `name` | *string* | ダウンタイム名 |
| `memo` | *string* | [optional] ダウンタイムのメモ |
| `start` | *number* | ダウンタイム開始時刻 (epoch秒) |
| `duration` | *number* | ダウンタイム期間 (分) |
| `recurrence` | *recurrence* | [optional] 繰り返し設定 |
| `serviceScopes` | *array[string]* | [optional] サービス対象スコープ。サービス名[*](#service-name-and-role-fullname)の配列 |
| `serviceExcludeScopes` | *array[string]* | [optional] サービス除外スコープ。サービス名[*](#service-name-and-role-fullname)の配列 |
| `roleScopes` | *array[string]* | [optional] ロール対象スコープ。ロール詳細名[*](#service-name-and-role-fullname)の配列 |
| `roleExcludeScopes` | *array[string]* | [optional] ロール除外スコープ。ロール詳細名[*](#service-name-and-role-fullname)の配列 |
| `monitorScopes` | *array[string]* | [optional] 監視設定対象スコープ。`<monitor id>`の配列 |
| `monitorExcludeScopes` | *array[string]* | [optional] 監視設定除外スコープ。`<monitor id>`の配列 |

`<recurrence>` は以下のキーをもつオブジェクト。

| KEY | TYPE | DESCRIPTION |
| --- | ---- | ----------- |
| `type` | *string* | 繰り返しの種類 (`hourly`, `daily`, `weekly`, `monthly`, `yearly`) |
| `interval` | *number* | 繰り返しの間隔 |
| `weekdays` | *array[string]* | [optional] 曜日設定 (`Sunday`, `Monday`, `Tuesday`, `Wednesday`, `Thursday`, `Friday`, `Saturday`)。 `type` が `weekly` のときのみ有効 |
| `until` | *number* | [optional] 繰り返し終了時刻 (epoch秒) |

### 応答
#### 成功時
`id` が付与されてダウンタイムが返却されます。

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
      <td>入力が不正だったとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>名前やメモが長すぎるとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>ダウンタイム期間が正ではないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>繰り返し設定が不正のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>スコープの対象サービス・ロール・監視設定が重複している、または存在しないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>スコープのサービス・ロール・監視設定が存在しないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h4 id="service-name-and-role-fullname">* サービス名とロール詳細名</h4>

サービス名は `<service name>` 、ロールの詳細名は `<service name>:<role name>` というフォーマットの文字列です。

<table class="eg-table">
  <tbody>
  <tr>
    <th>e.g.</th>
    <td>Hatena-Bookmark サービスなら<code>Hatena-Bookmark</code>Hatena-Bookmark サービスの db-master ロールなら<code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

使用できる文字列は `/^[A-Za-z0-9][A-Za-z0-9_-]+$/`。

----------------------------------------------

<h2 id="list">ダウンタイムの一覧</h2>
<p class="type-get">
  <code>GET</code>
  <code>/api/v0/downtimes</code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答
| KEY         | TYPE            | DESCRIPTION          |
| ----------- | --------------- | -------------------- |
| `downtimes` | *array[object]* | ダウンタイムのリスト |


----------------------------------------------

<h2 id="update">ダウンタイムの更新</h2>
<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力
[ダウンタイムの作成](#create)と同様の形式です。

### 応答
#### 成功時
更新後のダウンタイムを[ダウンタイムの作成](#create)と同様の形式で返します。

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
      <td>入力が不正だったとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>名前やメモが長すぎるとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>ダウンタイム期間が正ではないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>繰り返し設定が不正のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>スコープの対象サービス・ロール・監視設定が重複している、または存在しないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>スコープのサービス・ロール・監視設定が存在しないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://mackerel.io/ja/docs/entry/faq/organization/ip-restriction" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのダウンタイムがみつからないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">ダウンタイムの削除</h2>

指定されたIDのダウンタイムを削除します。

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答
#### 成功時
削除する直前のダウンタイムを[ダウンタイムの作成](#create)と同様の形式で返します。

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
      <td>指定されたIDのダウンタイムがみつからないとき</td>
    </tr>
  </tbody>
</table>
