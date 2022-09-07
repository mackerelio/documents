---
Title: ホスト
Date: 2016-03-18T18:51:02+09:00
URL: https://mackerel.io/ja/api-docs/entry/hosts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792367523201
---

<ul class="internal-nav">
  <li><a href="#create">ホスト情報の登録</a></li>
  <li><a href="#get">ホスト情報の取得</a></li>
  <li><a href="#update-information">ホスト情報の更新</a></li>
  <li><a href="#update-status">ホストのステータスの更新</a></li>
  <li><a href="#update-roles">ホストのロールの更新</a><li>
  <li><a href="#retire">ホストの退役</a></li>
  <li><a href="#bulk-retire">ホストの一括退役</a></li>
  <li><a href="#list">ホストの一覧</a></li>
  <li><a href="#metric-names">メトリック名の一覧</a></li>
  <li><a href="#monitored-statuses">監視ステータスの一覧</a></li>
</ul>

<h2 id="create">ホスト情報の登録</h2>

エージェントが稼動しているホストをMackerelに登録します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

入力にホストが所属しているロールが与えられたとき、ホストへのロールの割り当てが行われます。この際存在しないサービス・ロール名が与えられた場合、それらが新規作成されます。

以下のキーをもつオブジェクトです。

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `name` | *string* | ホスト名 |
| `displayName` | *string* | [optional] ホストの管理名 |
| `customIdentifier` | *string* | [optional] ホストに対するユーザー独自の、オーガニゼーションで一意な識別子[*1](#customIdentifier) |
| `memo` | *string* | [optional] ホストに関するメモ |
| `meta` | *object* | ホストのメタ情報[*2](#meta)。空のオブジェクトでも登録することができます。 |
| `interfaces` | *array[object]* | [optional] ホストのネットワークインターフェース情報[*3](#interfaces) |
| `roleFullnames` | *array[string]* | [optional] ホストが所属しているロールの名前の配列[*4](#roleFullnames) |
| `checks` | *array[hash[string]]* | [optional] ホストを監視するチェック監視項目の配列[*5](#checks) |

### 応答

#### 成功時

```json
{
  "id": <hostId>
}
```

<i>`<hostId>`</i> : このホストに割り当てられたホスト IDです。以降のリクエストではこの ID を用いてホストを識別します。

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
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h4 id="customIdentifier" class="annotation">*1 customIdentifier</h4>

<span class="table-code">customIdentifier</span> に `null` もしくは `""` （空文字）を指定した場合、HTTPリクエストメソッドにより、以下のような挙動となります。

**`POST` の場合**

`null` で登録されます。

**`PUT` の場合**

登録されている値が `null` で上書きされます。

<h4 id="meta" class="annotation">*2 meta</h4>

<span class="table-code">meta</span> には色々な情報が入ります。例えば、mackerel-agent やクラウドインテグレーションにより登録されたホストの場合は以下のような情報が登録されます。

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `agent-name` | *string* | エージェントの名前 e.g. "mackerel-agent/0.27.0 (Revision dfbccea)" |
| `agent-revision` | *string* | エージェントのリビジョン（Git のコミット SHA1） e.g. "2f531c6" |
| `agent-version` | *string* | エージェントのバージョン。 e.g. "0.4.2" |
| `block_device` | *hash[object]* | ブロックデバイスの情報。キーは名前、値は `size` などを持ったオブジェクトです。 |
| `cpu` | *array[object]* | 各 CPU コアの情報。それぞれの object が `mhz` や `model_name` を持ちます。 |
| `filesystem` | *hash[object]* | ファイルシステムの情報。キーはファイルシステムの名前、値は `kb_available`, `kb_used` などを持ったオブジェクトです。 |
| `kernel` | *hash[string]* | カーネルの情報。`uname` コマンドで得られるものです。 |
| `memory` | *hash[string]* | メモリの情報。|
| `cloud`  | *hash[object]* | [AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)などのクラウドインテグレーションにより登録されたホストの場合に存在します。`provider` `metadata` などを持ったオブジェクトです。 |

<h4 id="interfaces">*3 interfaces</h4>

<span class="table-code">interfaces</span>の一つの要素は以下のキーを持つオブジェクトです。

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | インターフェースの名前。 e.g. "eth0" |
| `macAddress` | *string* | [optional] インターフェースの MAC アドレス。 |
| `ipv4Addresses` | *array[string]* | [optional] インターフェースの IPv4 アドレス。 |
| `ipv6Addresses` | *array[string]* | [optional] インターフェースの IPv6 アドレス。 |
| `ipAddress` | *string* | [optional] インターフェースの IPv4 アドレス。 |
| `ipv6Address` | *string* | [optional] インターフェースの IPv6 アドレス。 |

ただし `ipAddress` と `ipv4Addresses`、または `ipv6Address` と `ipv6Addresses` が同時に指定された場合は、それぞれ `ipv4Addresses` `ipv6Addresses` が優先されます。

<h4 id="roleFullnames">*4 roleFullnames</h4>
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

<h4 id="checks">*5 checks</h4>
<span class="table-code">checks</span>の一つの要素は以下のキーを持つオブジェクトです。

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | チェック監視項目の名前（`[a-zA-Z0-9_-]+`）。 |
| `memo` | *string* | [optional] チェック監視項目のメモ。 |

----------------------------------------------

<h2 id="get">ホスト情報の取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "host": <host>
}
```

<i>`<host>`</i> は以下のキーを持つオブジェクトです。

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `createdAt` | *number* | ホストの登録時刻（epoch秒） |
| `id` | *string* | ホストID |
| `name` | *string* | ホスト名 |
| `displayName` | *string* | [optional] ホストの管理名 |
| `customIdentifier` | *string* | [optional] ホストに対するユーザー独自の、オーガニゼーションで一意な識別子 |
| `meta` | *object* | ホストのメタ情報 |
| `interfaces` | *array[object]* | ホストのネットワークインターフェース情報 |
| `size` | *string* | [ホストの種類](https://mackerel.io/ja/docs/entry/spec/about-host#host-kinds) （`"standard"` または `"micro"`） |
| `status` | *string* | ホストのステータス（`"working"`, `"standby"` など） |
| `memo` | *string* | ホストに関するメモ |
| `isRetired` | *boolean* | ホストが退役しているかどうか |
| `retiredAt` | *number* | [optional] ホストの退役時刻（epoch秒） |
| `roles` | *hash[array[string]]* | ホストが所属しているロールの一覧。キーはサービス名、値はそのサービスにおけるロール名の配列です。 |

----------------------------------------------

<h2 id="update-information">ホスト情報の更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

[ホスト情報の登録](#create)と同様です。

`roleFullnames` で指定しなかったホストがもともと所属しているロールには所属したままとなります。 ホストにもともと所属しているロールをはずしたい場合は [ホストのロールの更新](#update-roles) を使用します。

### 応答

#### 成功時

```json
{
  "id": <hostId>
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
      <td>404</td>
      <td><code><em>&lt;hostId&gt;</em></code>に対応するホストが見つからないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>JSON のフォーマットが不正であるとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update-status">ホストのステータスの更新</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/status</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

```json
{
  "status": <hostStatus>
}
```

<i>`<hostStatus>`</i> は "standby", "working", "maintenance", "poweroff" のいずれかです。

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
      <td>404</td>
      <td><code><em>&lt;hostId&gt;</em></code>に対応するホストが見つからないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>JSON のフォーマットが不正であるとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update-roles">ホストのロールの更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/role-fullnames</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-write">Write</li>
</ul>

### 入力

```json
{
  "roleFullnames": [ <string>, <string>, … ]
}
```

`roleFullnames` は <a href="#create">ホスト情報の登録</a>の項目の `roleFullnames` と同じ。

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
      <td>404</td>
      <td>hostId に対応するホストが見つからないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>JSON のフォーマットが不正であるとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>roleFullnames</code> のフォーマットが不正であるとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="retire">ホストの退役</h2>

登録したホストを退役状態にします。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/retire</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

空のJSONをPOSTします。

```json
{}
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
      <td>404</td>
      <td><code><em>&lt;hostId&gt;</em></code>に対応するホストが見つからないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>JSON のフォーマットが不正であるとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="bulk-retire">ホストの一括退役</h2>

登録したホストを複数個まとめて退役状態にします。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts/bulk-retire</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

<code><em>&lt;hostId&gt;</em></code> を最大 30 個まで指定できます。

```json
{
  "ids": [ <hostId>, <hostId>, … ]
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
      <td>404</td>
      <td>1つでも<code><em>&lt;hostId&gt;</em></code>に対応するホストが見つからないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>JSON のフォーマットが不正であるとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>一度に退役できるホスト数（30個）の制限を超えて退役しようとしたとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="list">ホストの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts</code>
</p>

`/api/v0/hosts.json` も同じクエリパラメータで同じ内容を返します。

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力（クエリパラメータ）

次のパラメータでホストを絞り込むことができます。なにも指定されていない場合は、全ホストが返却されます。

| PARAM     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `service` | *string* | [optional] サービス名。 |
| `role` | *string* | [optional] サービス内のロール名。複数指定可能（結果は各ロールに所属するホスト群の和集合となります）。`service`が指定されていない場合は無効です。 |
| `name` | *string* | [optional] ホスト名。 |
| `status` | *string* | [optional] ホストのステータスを絞り込む。複数指定可能。デフォルトは `working` と `standby`です。 |
| `customIdentifier`    | *string*        | [optional] [ホスト情報の登録](#create)や[ホスト情報の更新](#update-information)で登録したユーザー独自のオーガニゼーションで一意な識別子。 |


### 応答

```json
{
  "hosts": [ <host>, <host>, …]
}
```
<i>`<host>`</i> は[ホスト情報の取得](#get)で返ってくるオブジェクトと同型です。

----------------------------------------------

<h2 id="metric-names">メトリック名の一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metric-names</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "names": [<metricName>, <metricName>, ...]
}
```

| KEY      | TYPE            | DESCRIPTION                              |
| -------- | ------          | -----------                              |
| `names`  | *array[string]* | ホストに投稿されているメトリック名。         |

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
      <td><code><em>&lt;hostId&gt;</em></code>に対応するホストが見つからないとき</td>
    </tr>
  </tbody>
</table>

<h2 id="monitored-statuses">監視ステータスの一覧</h2>

ホストに紐付いた監視ルールとそのステータス（監視ステータス）を取得します。対象は死活監視、メトリック監視、チェック監視、ロール内異常検知です。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/monitored-statuses</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "monitoredStatuses": [<monitoredStatus>, <monitoredStatus>, ...]
}
```

<i>`<monitoredStatus>`</i> は以下のキーを持つ、監視ステータスを表すオブジェクトです。

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `monitorId`  | *string* | 監視ルールのID |
| `status` | *string* | アラートステータス。`"OK"`、 `"CRITICAL"`、 `"WARNING"`、 `"UNKNOWN"` のいずれかになります。 |
| `detail` | *string* | [optional] 詳細情報[*6](#list-detail)|

<h4 id="list-detail" class="annotation">*6 detail</h4>

監視ステータスに付随する詳細情報です。現在はチェック監視の監視ステータスにのみ付加されます。

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `type` | *string* | 詳細情報の種類。チェック監視の場合、常に `check` です。 |
| `message` | *string* | コマンド出力結果などの補助的なテキスト |
| `memo` | *string* | [optional] チェック監視に設定されたメモ |


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
      <td>指定されたIDのホストがみつからないとき</td>
    </tr>
  </tbody>
</table>
