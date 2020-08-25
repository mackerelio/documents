---
Title: 監視ルール
Date: 2016-03-22T10:42:05+09:00
URL: https://mackerel.io/ja/api-docs/entry/monitors
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368066094
---

<ul class="internal-nav">
  <li><a href="#create">監視ルールの登録</a></li>
  <li><a href="#list">監視ルールの一覧</a></li>
  <li><a href="#get">監視ルールの取得</a></li>
  <li><a href="#update">監視ルールの更新</a></li>
  <li><a href="#delete">監視ルールの削除</a></li>
</ul>

<h2 id="create">監視ルールの登録</h2>

各種メトリックに対する監視や、外形監視をMackerelに登録します。
監視対象によって入力が異なります。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/monitors</code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

<ul class="internal-nav create">
  <li><a href="#create-host-metric-monitoring">ホストメトリック監視</a></li>
  <li><a href="#create-connectivity-monitoring">ホスト死活監視</a></li>
  <li><a href="#create-service-metric-monitoring">サービスメトリック監視</a></li>
  <li><a href="#create-external-monitoring">外形監視</a></li>
  <li><a href="#create-expression-monitoring">式による監視</a></li>
  <li><a href="#create-anomaly-detection-monitoring">ロール内異常検知による監視</a></li>
</ul>

<h3 id="create-host-metric-monitoring">ホストメトリック監視</h3>

#### 入力(ホストメトリック監視)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | 定数文字列 `"host"`               |
| `name`          | *string*   | 監視一覧などで参照できる任意の名称。   |
| `memo`          | *string*   | [optional] 監視ルールのメモ。 |
| `duration`      | *number*   | 指定された間隔(分)の平均値を監視します。有効範囲：1~10分。 |
| `metric`        | *string*   | 監視対象のホストメトリック名。特定の定数文字列を指定することで、割合監視が可能です。 [*1](#comparative-monitoring) |
| `operator`      | *string*   | 指定した数値より大きいか小さいかというアラート条件を指定。`">"` または `"<"`。左辺が観測値で右辺が設定値となります。|
| `warning`       | *number*   | warningのAlert発生の閾値。割合監視 [*1](#comparative-monitoring) の場合は、有効範囲が0~100(%)になります。 |
| `critical`      | *number*   | criticalのAlert発生の閾値。割合監視 [*1](#comparative-monitoring) の場合は、有効範囲が0~100(%)になります。 |
| `maxCheckAttempts`     | *number* | [optional] 何回連続で Warning/Critical になったらアラートを発生させるか。デフォルトは1 (1~10)です。 |
| `notificationInterval` | *number* | [optional] 通知の再送設定をするときの再送間隔 (分)。このフィールドを省略すると通知は再送されません。 |
| `scopes`        | *array[string]* | [optional] 監視対象のサービス名またはロール詳細名。[*2](#service-name)  |
| `excludeScopes` | *array[string]* | [optional] 監視除外対象のサービス名またはロール詳細名。[*2](#service-name)  |
| `isMute`        | *boolean*       | [optional] 監視がミュート状態か否か [*3](#mute) |

##### 入力例

```json
{
  "type": "host",
  "name": "disk.aa-00.writes.delta",
  "memo": "This monitor is for Hatena Blog.",
  "duration": 3,
  "metric": "disk.aa-00.writes.delta",
  "operator": ">",
  "warning": 20000.0,
  "critical": 400000.0,
  "maxCheckAttempts": 3,
  "notificationInterval": 60,
  "scopes": [
    "Hatena-Blog"
  ],
  "excludeScopes": [
    "Hatena-Bookmark: db-master"
  ]
}
```

#### 応答(ホストメトリック監視)

##### 成功時

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "host",
  "name": "disk.aa-00.writes.delta",
  "memo": "This monitor is for Hatena Blog.",
  "duration": 3,
  "metric": "disk.aa-00.writes.delta",
  "operator": ">",
  "warning": 20000.0,
  "critical": 400000.0,
  "maxCheckAttempts": 3,
  "notificationInterval": 60,
  "scopes": [
    "Hatena-Blog"
  ],
  "excludeScopes": [
    "Hatena-Bookmark: db-master"
  ]
}
```

`id` が付与されて返却されます。

##### 失敗時

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
      <td><code>name</code>が空文字列のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>memo</code>が250文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>duration</code>が1~10の範囲を越えたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>割合監視 <a href="#comparative-monitoring">*1</a> の設定で、<code>warning</code>または<code>critical</code>が0~100(%)の範囲を越えたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>maxCheckAttempts</code>が1~10の範囲を越えたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>scope</code>や<code>excludeScopes</code>に指定されているサービス名やロール詳細名が未登録のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>再送間隔が10分以上でないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h4 id="comparative-monitoring" class="annotation">*1 割合監視</h4>

ホストメトリック監視の場合、`metric` に特定の文字列を指定することでメトリックに対する割合監視となります。
割合監視として指定可能な `metric` は下記のとおりです。

| metric             |
|--------------------|
| `"cpu%"`           |
| `"memory%"`        |
| `"disk%"`          |
| `"swap%"`          |

<h4 id="service-name">*2 サービス名またはロール詳細名</h4>

サービス名またはロールの詳細な名前は `<service name>` または `<service name>:<role name>` というフォーマットの文字列です。

<table class="eg-table">
  <tbody>
  <tr>
    <th>e.g.</th>
    <td>Hatena-Bookmark サービスなら<code>Hatena-Bookmark</code>Hatena-Bookmark サービスの db-master ロールなら<code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

使用できる文字列は `/^[A-Za-z0-9][A-Za-z0-9_-]+$/`。

<h4 id="mute">*3 監視のミュート</h4>

監視における通知を行わないようにする機能です。 監視の閾値に応じてアラートは発生しますが、通知チャンネルへの通知は送信されません。

<h3 id="create-connectivity-monitoring">ホスト死活監視</h3>

#### 入力(ホスト死活監視)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | 定数文字列 `"connectivity"`               |
| `name`          | *string*   | [optional] 監視一覧などで参照できる任意の名称。デフォルトは`connectivity`です。   |
| `memo`          | *string*   | [optional] 監視ルールのメモ。 |
| `alertStatusOnGone`        | *string*       | [optional] この監視ルールで発生するアラートのアラートステータス。`"CRITICAL"`（デフォルト値）または `"WARNING"` です。|
| `scopes`        | *array[string]* | [optional] 監視対象のサービス名またはロール詳細名。[*2](#service-name)  |
| `excludeScopes` | *array[string]* | [optional] 監視除外対象のサービス名またはロール詳細名。[*2](#service-name)  |
| `notificationInterval` | *number* | [optional] 通知の再送設定をするときの再送間隔 (分)。このフィールドを省略すると通知は再送されません。 |
| `isMute`        | *boolean*       | [optional] 監視がミュート状態か否か |

##### 入力例

```json
{
  "type": "connectivity",
  "name": "connectivity service1",
  "memo": "A monitor that checks connectivity.",
  "alertStatusOnGone": "WARNING",
  "scopes": [
    "service1"
  ],
  "excludeScopes": [
    "service1: role3"
  ]
}
```

#### 応答(ホスト死活監視)

##### 成功時

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "connectivity",
  "name": "connectivity service1",
  "memo": "A monitor that checks connectivity.",
  "alertStatusOnGone": "WARNING",
  "scopes": [
    "service1"
  ],
  "excludeScopes": [
    "service1: role3"
  ]
}
```

`id` が付与されて返却されます。

##### 失敗時

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
      <td><code>name</code>が空文字列のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>memo</code>が250文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>alertStatusOnGone</code>が<code>CRITICAL</code>または<code>WARNING</code>でないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>scope</code>や<code>excludeScopes</code>に指定されているサービス名やロール詳細名が未登録のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>再送間隔が10分以上でないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h3 id="create-service-metric-monitoring">サービスメトリック監視</h3>

#### 入力(サービスメトリック監視)

| KEY                       | TYPE      | DESCRIPTION                                                                                                          |
| ------------------------- | --------- | -------------------------------------------------------------------------------------------------------------------- |
| `type`                    | *string*  | 定数文字列 `"service"`                                                                                               |
| `name`                    | *string*  | 監視一覧などで参照できる任意の名称。                                                                                 |
| `memo`                    | *string*  | [optional] 監視ルールのメモ。                                                                                          |
| `service`                 | *string*  | 監視対象となるサービス名。                                                                                           |
| `duration`                | *number*  | 指定されたポイント数の平均値を監視する。有効範囲：直近1~10ポイント                                                   |
| `metric`                  | *string*  | 監視対象のホストメトリック名。                                                                                       |
| `operator`                | *string*  | 指定した数値より大きいか小さいかというアラート条件を指定。`">"` または `"<"`。左辺が観測値で右辺が設定値となります。 |
| `warning`                 | *number*  | warningのAlert発生の閾値。                                                                                           |
| `critical`                | *number*  | criticalのAlert発生の閾値。                                                                                          |
| `maxCheckAttempts`        | *number*  | [optional] 何回連続で Warning/Critical になったらアラートを発生させるか。デフォルトは1 (1~10)です。                  |
| `missingDurationWarning`  | *number*  | [optional] 途切れ監視のwarningのAlert発生閾値 (分)。                                                                 |
| `missingDurationCritical` | *number*  | [optional] 途切れ監視のcriticalのAlert発生閾値 (分)。                                                                |
| `notificationInterval`    | *number*  | [optional] 通知の再送設定をするときの再送間隔 (分)。このフィールドを省略すると通知は再送されません。                 |
| `isMute`                  | *boolean* | [optional] 監視がミュート状態か否か                                                                                  |

##### 入力例

```json
{
  "type": "service",
  "name": "Hatena-Blog - access_num.4xx_count",
  "memo": "A monitor that checks the number of 4xx for Hatena Blog",
  "service": "Hatena-Blog",
  "duration": 1,
  "metric": "access_num.4xx_count",
  "operator": ">",
  "warning": 50.0,
  "critical": 100.0,
  "maxCheckAttempts": 3,
  "missingDurationWarning": 360,
  "missingDurationCritical": 720,
  "notificationInterval": 60
}
```

#### 応答(サービスメトリック監視)

##### 成功時

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "service",
  "name": "Hatena-Blog - access_num.4xx_count",
  "memo": "A monitor that checks the number of 4xx for Hatena Blog",
  "service": "Hatena-Blog",
  "duration": 1,
  "metric": "access_num.4xx_count",
  "operator": ">",
  "warning": 50.0,
  "critical": 100.0,
  "maxCheckAttempts": 3,
  "missingDurationWarning": 360,
  "missingDurationCritical": 720,
  "notificationInterval": 60
}
```

`id` が付与されて返却されます。

##### 失敗時

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
      <td><code>name</code>が空文字列のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>memo</code>が250文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>duration</code>が1~10の範囲を越えたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>maxCheckAttempts</code>が1~10の範囲を越えたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>missingDurationWarning</code>または<code>missingDurationCritical</code>が10分の倍数ではないとき、あるいは一週間を超える時</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>service</code>に指定されているサービス名が未登録のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>再送間隔が10分以上でないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h3 id="create-external-monitoring">外形監視</h3>

#### 入力(外形監視)

| KEY                               | TYPE       | DESCRIPTION                      |
| ----------------------            | ---------- | -------------------------------- |
| `type`                            | *string*   | 定数文字列 `"external"`          |
| `name`                            | *string*   | 監視一覧などで参照できる任意の名称。   |
| `memo`                            | *string*   | [optional] 監視ルールのメモ。 |
| `url`                             | *string*   | 監視対象のURL。 |
| `method`                          | *string*   | [optional] リクエスト時のメソッド。`GET`, `POST`, `PUT`, `DELETE`のいずれかで、省略時は`GET` |
| `service`                         | *string*   | [optional] サービス名(ここで関連づけたサービスのサービスメトリックに監視時のレスポンスタイムがグラフ化されます) |
| `notificationInterval`            | *number*   | [optional] 通知の再送設定をするときの再送間隔 (分)。このフィールドを省略すると通知は再送されません。 |
| `responseTimeWarning`             | *number*   | [optional] warningのAlert発生の応答時間の閾値 (ミリ秒) `service` 指定が必要です。|
| `responseTimeCritical`            | *number*   | [optional] criticalのAlert発生の応答時間の閾値 (ミリ秒) `service` 指定が必要です。 |
| `responseTimeDuration`            | *number*   | [optional] 指定された期間のリクエストの平均値を監視する。(1~10分)  `service` 指定が必要です。|
| `containsString`                  | *string*   | [optional] レスポンスボディに含まれているべき文字列。 |
| `maxCheckAttempts`                | *number*   | [optional] 何回連続で Warning/Critical になったらアラートを発生させるか。デフォルトは1 (1~10)です。 |
| `certificationExpirationWarning`  | *number*   | [optional] 証明書の有効期限切れ監視のWaning閾値。期限までの残り日数。 |
| `certificationExpirationCritical` | *number*   | [optional] 証明書の有効期限切れ監視のCritical閾値。期限までの残り日数。 |
| `skipCertificateVerification`     | *boolean*  | [optional] 証明書の照合を行わなわずに監視をおこなう。 |
| `isMute`                          | *boolean*  | [optional] 監視がミュート状態か否か |
| `headers`                         | *array[object]* | [optional] HTTPリクエストヘッダとして設定されているべき値を`name`と`value`で指定。このフィールドを省略するとデフォルトのヘッダが設定されます。ヘッダを設定したくない場合は空の配列を指定してください。|
| `requestBody`                     | *string*   | [optional] リクエスト時のメッセージボディ |

応答時間の監視を行うには `responseTimeWarning`, `responseTimeCritical`, `responseTimeDuration` の全てを指定する必要があります。
証明書有効期限の監視を行うには `certificationExpirationWarning`, `certificationExpirationCritical` の両方を指定する必要があります。

##### 入力例

```json
{
  "type": "external",
  "name": "Example Domain",
  "memo": "Monitors example.com",
  "method": "GET",
  "url": "https://example.com",
  "service": "Hatena-Blog",
  "notificationInterval": 60,
  "responseTimeWarning": 5000,
  "responseTimeCritical": 10000,
  "responseTimeDuration": 3,
  "containsString": "Example",
  "maxCheckAttempts": 3,
  "certificationExpirationWarning": 90,
  "certificationExpirationCritical": 30,
  "isMute": false,
  "headers": [{"name":"Cache-Control", "value":"no-cache"}]
}
```

#### 応答(外形監視)

##### 成功時

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "external",
  "name": "example.com",
  "memo": "Monitors example.com",
  "method": "GET",
  "url": "https://example.com",
  "service": "Hatena-Blog",
  "notificationInterval": 60,
  "responseTimeWarning": 5000,
  "responseTimeCritical": 10000,
  "responseTimeDuration": 3,
  "containsString": "Example",
  "maxCheckAttempts": 3,
  "certificationLimitWarning": 90,
  "certificationLimitCritical": 30,
  "isMute": false,
  "headers": [{"name":"Cache-Control", "value":"no-cache"}]
}
```

`id` が付与されて返却されます。

##### 失敗時

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
      <td><code>name</code>が空文字列のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>memo</code>が250文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>url</code>のスキームがhttp, https以外だったとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>再送間隔が10分以上でないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>maxCheckAttempts</code>が1~10の範囲を越えたとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h3 id="create-expression-monitoring">式による監視</h3>

#### 入力(式による監視)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | 定数文字列 `"expression"`               |
| `name`          | *string*   | 監視一覧などで参照できる任意の名称。   |
| `memo`          | *string*   | [optional] 監視ルールのメモ。 |
| `expression`    | *string*   | 監視対象の式。グラフの系列が1本になるもののみ有効。 |
| `operator`      | *string*   | 指定した数値より大きいか小さいかというアラート条件を指定。`">"` または `"<"`。左辺が観測値で右辺が設定値となります。|
| `warning`       | *number*   | warningのAlert発生の閾値。 |
| `critical`      | *number*   | criticalのAlert発生の閾値。 |
| `notificationInterval` | *number* | [optional] 通知の再送設定をするときの再送間隔 (分)。このフィールドを省略すると通知は再送されません。 |
| `isMute`        | *boolean*       | [optional] 監視がミュート状態か否か [*3](#mute) |

##### 入力例

```json
{
  "type": "expression",
  "name": "role average",
  "memo": "Monitors the average of loadavg5",
  "expression": "avg(roleSlots(\"server:role\",\"loadavg5\"))",
  "operator": ">",
  "warning": 5.0,
  "critical": 10.0,
  "notificationInterval": 60
}
```

#### 応答(式による監視)

##### 成功時

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "expression",
  "name": "role average",
  "memo": "Monitors the average of loadavg5",
  "expression": "avg(roleSlots(\"server:role\",\"loadavg5\"))",
  "operator": ">",
  "warning": 5.0,
  "critical": 10.0,
  "notificationInterval": 60
}
```

`id` が付与されて返却されます。

##### 失敗時

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
      <td><code>name</code>が空文字列のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>memo</code>が250文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>再送間隔が10分以上でないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>無効な式が指定されたとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h3 id="create-anomaly-detection-monitoring">ロール内異常検知による監視</h3>

#### 入力(ロール内異常検知による監視)

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `type`          | *string*   | 定数文字列 `"anomalyDetection"`               |
| `name`          | *string*   | 監視一覧などで参照できる任意の名称。  |
| `memo`          | *string*   | [optional] 監視ルールのメモ。 |
| `scopes`        | *array[string]* | 監視対象のサービス名とロール詳細名。[*2](#service-name)  |
| `warningSensitivity`       | *string*   | warningのAlert発生の閾値。`insensitive`、`normal`、`sensitive`のいずれか。 |
| `criticalSensitivity`       | *string*   | criticalのAlert発生の閾値。`insensitive`、`normal`、`sensitive`のいずれか。 |
| `maxCheckAttempts`     | *number* | [optional] 何回連続で Warning/Critical になったらアラートを発生させるか。デフォルトは3 (1~10)です。 |
| `trainingPeriodFrom`     | *number* | [optional] 再学習させる際に起点となる時刻(epoch秒)。 |
| `notificationInterval` | *number* | [optional] 通知の再送設定をするときの再送間隔 (分)。このフィールドを省略すると通知は再送されません。 |
| `isMute`        | *boolean*       | [optional] 監視がミュート状態か否か |

##### 入力例

```json
{
  "type": "anomalyDetection",
  "name": "anomaly detection",
  "memo": "my anomaly detection for roles",
  "scopes": [
    "myService: myRole"
  ],
  "warningSensitivity": "insensitive",
  "maxCheckAttempts": 3
}
```

#### 応答(ロール内異常検知による監視)

##### 成功時

```json
{
  "id"  : "2cSZzK3XfmG",
  "type": "anomalyDetection",
  "name": "anomaly detection",
  "memo": "my anomaly detection for roles",
  "scopes": [
    "myService: myRole"
  ],
  "warningSensitivity": "insensitive",
  "maxCheckAttempts": 3
}
```

`id` が付与されて返却されます。

##### 失敗時

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
      <td><code>name</code>が空文字列のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>memo</code>が250文字を超えているとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>scopes</code>に指定されているサービス名やロール詳細名が未登録のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>warningSensitivity</code>または<code>criticalSensitivity</code>に <code>insensitive</code> / <code>normal</code> / <code>sensitive</code> 以外の値が指定されたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td>再送間隔が10分以上でないとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>trainingPeriodFrom</code>が未来の値のとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="list">監視ルールの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/monitors</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "monitors": [
    {
      "id"  : "2cSZzK3XfmB",
      "type": "host",
      "name": "disk.aa-00.writes.delta",
      "memo": "This monitor is for Hatena Blog.",
      "duration": 3,
      "metric": "disk.aa-00.writes.delta",
      "operator": ">",
      "warning": 20000.0,
      "critical": 400000.0,
      "maxCheckAttempts": 3,
      "scopes": [
        "Hatena-Blog"
      ],
      "excludeScopes": [
        "Hatena-Bookmark: db-master"
      ]
    },
    {
      "id": "2cSZzK3XfmA",
      "type": "connectivity",
      "alertStatusOnGone": "CRITICAL",
      "scopes": [],
      "excludeScopes": []
    },
    {
      "id"  : "2cSZzK3XfmC",
      "type": "service",
      "name": "Hatena-Blog - access_num.4xx_count",
      "memo": "A monitor that checks the number of 4xx for Hatena Blog",
      "service": "Hatena-Blog",
      "duration": 1,
      "metric": "access_num.4xx_count",
      "operator": ">",
      "warning": 50.0,
      "critical": 100.0,
      "maxCheckAttempts": 1,
      "notificationInterval": 60
    },
    {
      "id"  : "2cSZzK3XfmD",
      "type": "external",
      "name": "example.com",
      "memo": "Monitors example.com",
      "url": "http://www.example.com",
      "service": "Hatena-Blog",
      "headers": [{"name":"Cache-Control", "value":"no-cache"}],
      "maxCheckAttempts": 1
    },
    {
      "id"  : "2cSZzK3XfmE",
      "type": "expression",
      "name": "role average",
      "memo": "Monitors the average of loadavg5",
      "expression": "avg(roleSlots(\"server:role\",\"loadavg5\"))",
      "operator": ">",
      "warning": 5.0,
      "critical": 10.0,
      "notificationInterval": 60
    }
  ]
}
```

- 各項目は[監視ルールの登録](#create)と同様です
- 並び順はモニター種別 -> 名前の順 (mackerel.ioの監視一覧画面と同様)です

----------------------------------------------

<h2 id="get">監視ルールの取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "monitor": {
    "id"  : "2cSZzK3XfmB",
    "type": "host",
    "name": "disk.aa-00.writes.delta",
    "memo": "This monitor is for Hatena Blog.",
    "duration": 3,
    "metric": "disk.aa-00.writes.delta",
    "operator": ">",
    "warning": 20000.0,
    "critical": 400000.0,
    "maxCheckAttempts": 3,
    "scopes": [
      "Hatena-Blog"
    ],
    "excludeScopes": [
      "Hatena-Bookmark: db-master"
    ]
  }
}
```

- 各項目は[監視ルールの登録](#create)と同様です

----------------------------------------------

<h2 id="update">監視ルールの更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
</p>

リクエストとレスポンスは[監視ルールの登録](#create)と同様です。不足している項目があると必須項目の場合はエラーとなります。
`scopes`と`excludeScopes`の更新は、指定したJsonで完全に上書きされます。たとえば、`scopes`がすでに存在している場合に項目を省略すると、`scopes`は削除されます。

### 死活監視

`alertStatusOnGone` フィールドを変更した時、変更前にその監視ルールによって発生したアラートは次のようになります。

- 通知の再送設定（`notificationInterval`）がされているアラート

    `alertStatusOnGone` の変更後に通知の再送が発生した場合にのみ、そのタイミングで新しいアラートステータスに変更されます。  

- 通知の再送設定がされていないアラート

    アラートステータスは変更されません。

また、`alertStatusOnGone` フィールドを指定しなかった場合、 `alertStatusOnGone` フィールドの値は更新されません。

### 外形監視

`headers` フィールドを指定しなかった場合、`headers` フィールドの値は更新されません。ヘッダの設定を削除したい場合は空の配列を指定してください。

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時

更新後の監視ルールを返します。[監視ルールの登録](#create) と同様の形式です。

#### 失敗時

[監視ルールの登録](#create)と同様のエラーです。

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
      <td>typeを変更しようとしたとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>name</code>が空文字列のとき</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>memo</code>が250文字を超えているとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>クエリパラメータに指定された<code>&lt;monitorId&gt;</code>が存在しない監視ルールだったとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">監視ルールの削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時

削除される直前の監視ルールの状態が返却されます。 形式は[監視ルールの登録](#create)と同様です。

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
      <td>クエリパラメータに指定された<code><em>&lt;monitorId&gt;</em></code>が存在しない監視ルールだったとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
