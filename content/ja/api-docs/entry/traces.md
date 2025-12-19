---
Title: トレース
Date: 2025-07-24T12:50:00+09:00
URL: https://mackerel.io/ja/api-docs/entry/traces
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/6802418398526653045
---

<ul class="internal-nav">
  <li><a href="#list">トレース一覧の取得</a></li>
  <li><a href="#get">トレースの取得</a></li>
</ul>

<h2 id="list">トレース一覧の取得</h2>

指定された条件でトレースの一覧を取得します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/traces</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力

| KEY                    | TYPE             | DESCRIPTION                                                                 |
| ---------------------- | ---------------- | --------------------------------------------------------------------------- |
| `serviceName`          | *string*         | サービス名                                                                  |
| `serviceNamespace`     | *string*         | [optional] サービスの名前空間                                               |
| `from`                 | *number*         | トレース検索開始時刻(epoch秒)                                         |
| `to`                   | *number*         | トレース検索終了時刻(epoch秒)                                         |
| `environment`          | *string*         | [optional] 環境名                                                           |
| `traceId`              | *string*         | [optional] トレースID(16進数文字列32桁)                                    |
| `spanName`             | *string*         | [optional] スパン名                                                         |
| `version`              | *string*         | [optional] バージョン                                                       |
| `issueFingerprint`     | *string*         | [optional] Issueのフィンガープリント                                        |
| `statusCode`           | *string*         | [optional] Spanのステータスのコード。 `ERROR`または`OK` のどちらか             |
| `minLatencyMillis`     | *number*         | [optional] 最小レイテンシー(ミリ秒)                                         |
| `maxLatencyMillis`     | *number*         | [optional] 最大レイテンシー(ミリ秒)                                         |
| `attributes`           | *array[object]*  | [optional] 属性フィルタ条件のリスト                                         |
| `resourceAttributes`   | *array[object]*  | [optional] リソース属性フィルタ条件のリスト                                 |
| `page`                 | *number*         | [optional] ページ番号(1から始まる)。デフォルトは1                          |
| `perPage`              | *number*         | [optional] 1ページあたりの件数(1~100)。デフォルトは20                      |
| `order`                | *object*         | [optional] ソート条件                                                       |

属性フィルタオブジェクトは以下のキーを持ちます。

| KEY        | TYPE     | DESCRIPTION                                                                                     |
| ---------- | -------- | ----------------------------------------------------------------------------------------------- |
| `key`      | *string* | 属性のキー                                                                                      |
| `value`    | *string* | 属性の値(文字列として指定)                                                                     |
| `operator` | *string* | 比較演算子。`EQ`, `NEQ`, `GT`, `GTE`, `LT`, `LTE`, `STARTS_WITH` のいずれか            |
| `type`     | *string* | 属性値の型。`string`, `int`, `double`, `bool` のいずれか                                       |

属性の `type` によって利用可能な `operator` が異なります。

| operator      | string | int | double | bool |
| ------------- | ------ | --- | ------ | ---- |
| `EQ`          | ○      | ○   | ○      | ○    |
| `NEQ`         | ○      | ×   | ×      | ○    |
| `GT`          | ×      | ○   | ○      | ×    |
| `GTE`         | ×      | ○   | ○      | ×    |
| `LT`          | ×      | ○   | ○      | ×    |
| `LTE`         | ×      | ○   | ○      | ×    |
| `STARTS_WITH` | ○      | ×   | ×      | ×    |

ソート条件オブジェクトは以下のキーを持ちます。

| KEY         | TYPE     | DESCRIPTION                                                  |
| ----------- | -------- | ------------------------------------------------------------ |
| `column`    | *string* | [optional] ソート列。`LATENCY` または `START_AT`。デフォルトは `START_AT` |
| `direction` | *string* | [optional] ソート順。`ASC` または `DESC`。デフォルトは `DESC`            |

#### 入力例

```json
{
  "serviceName": "shoppingcart",
  "serviceNamespace": "shop",
  "from": 1718802000,
  "to": 1718888400,
  "environment": "production",
  "statusCode": "ERROR",
  "minLatencyMillis": 1000,
  "maxLatencyMillis": 5000,
  "attributes": [
    {
      "key": "http.status_code",
      "value": "500",
      "operator": "EQ",
      "type": "int"
    },
    {
      "key": "http.method",
      "value": "GET",
      "operator": "EQ",
      "type": "string"
    }
  ],
  "resourceAttributes": [
    {
      "key": "host.name",
      "value": "server",
      "operator": "CONTAINS",
      "type": "string"
    }
  ],
  "page": 1,
  "perPage": 20,
  "order": {
    "column": "START_AT",
    "direction": "DESC"
  }
}
```

### 応答

#### 成功時

```json
{
  "results": [
    {
      "traceId": "550e8400e29b41d4a716446655440000",
      "serviceName": "shoppingcart",
      "serviceNamespace": "shop",
      "environment": "production",
      "title": "GET /api/users",
      "traceStartAt": 1718802000,
      "traceLatencyMillis": 1234,
      "serviceStartAt": 1718802100,
      "serviceLatencyMillis": 567
    }
  ],
  "hasNextPage": true
}
```

レスポンスは以下のキーを持ちます。

| KEY           | TYPE    | DESCRIPTION                      |
| ------------- | ------- | -------------------------------- |
| `results`     | *array* | トレースのリスト                 |
| `hasNextPage` | *boolean* | 次のページが存在するか         |

トレースオブジェクトは以下のキーを持ちます。

| KEY                     | TYPE     | DESCRIPTION                                    |
| ----------------------- | -------- | ---------------------------------------------- |
| `traceId`               | *string* | トレースID(16進数文字列32桁)                  |
| `serviceName`           | *string* | サービス名                                     |
| `serviceNamespace`      | *string* | サービスの名前空間                             |
| `environment`           | *string* | 環境名                                         |
| `title`                 | *string* | トレースのタイトル(ルートスパンの名前など)    |
| `traceStartAt`          | *number* | トレース開始時刻(epoch秒)                |
| `traceLatencyMillis`    | *number* | トレース全体のレイテンシー(ミリ秒)            |
| `serviceStartAt`        | *number* | サービスのスパン開始時刻(epoch秒)        |
| `serviceLatencyMillis`  | *number* | サービスのスパンレイテンシー(ミリ秒)          |

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
      <td>無効なリクエストのとき</td>
    </tr>
    <tr>
      <td>401</td>
      <td>APIキーが無効なとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td><a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>429</td>
      <td>レート制限を超過したとき（1秒あたり1リクエスト）</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">トレースの取得</h2>

指定したトレースIDのトレース詳細情報を取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/traces/<em>&lt;traceId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "spans": [
    {
      "traceId": "0123456789abcdef0123456789abcdef",
      "spanId": "012345678012345678",
      "traceState": "congo=xx,key=val",
      "name": "test-span",
      "kind": "internal",
      "startTime": "2025-07-09T14:03:02.000Z",
      "endTime": "2025-07-09T14:03:02.000Z",
      "attributes": [
        {
          "key": "http.route",
          "value": {
            "valueType": "string",
            "stringValue": "/"
          }
        }
      ],
      "droppedAttributesCount": 0,
      "events": [
        {
          "time": "2025-07-09T14:03:02.000Z",
          "name": "event1",
          "attributes": [],
          "droppedAttributesCount": 0
        }
      ],
      "droppedEventsCount": 0,
      "links": [
        {
          "traceId": "abcdef0123456789abcdef0123456789",
          "spanId": "abcdefabcdef0102",
          "traceState": "",
          "attributes": [],
          "droppedAttributesCount": 0
        }
      ],
      "droppedLinksCount": 0,
      "status": {
        "message": "status message",
        "code": "ok"
      },
      "resource": {
        "attributes": [],
        "droppedAttributesCount": 0
      },
      "scope": {
        "name": "my-library",
        "version": "1.0.0",
        "attributes": [],
        "droppedAttributesCount": 0
      }
    }
  ]
}
```

トレース詳細オブジェクトは以下のキーを持ちます。

| KEY      | TYPE           | DESCRIPTION                                    |
| -------- | ------         | -----------                                    |
| `spans`  | *array*        | トレースに含まれるスパンの配列。               |

スパンオブジェクトは以下のキーを持ちます。

| KEY                      | TYPE     | DESCRIPTION                                   |
| --------                 | ------   | -----------                                   |
| `traceId`                | *string* | トレースのID。                                |
| `spanId`                 | *string* | スパンのID。                                  |
| `traceState`             | *string* | W3C Trace Contextのtracestate                 |
| `parentSpanId`           | *string* | [optional] 親スパンのID。ルートスパンの場合は省略。 |
| `name`                   | *string* | スパン名。                                    |
| `kind`                   | *string* | スパン種類の文字列(unspecified, internal, server, client producer, consumer)。 |
| `startTime`              | *string* | スパンの開始時刻。`YYYY-mm-ddTHH:MM:SS.sss+Z` |
| `endTime`                | *number* | スパンの終了時刻。`YYYY-mm-ddTHH:MM:SS.sss+Z` |
| `attributes`             | *array*  | スパン属性の配列。                            |
| `droppedAttributesCount` | *number* | ドロップされた属性の数。                      |
| `events`                 | *array*  | スパンイベントの配列。                        |
| `droppedEventsCount`     | *number* | ドロップされたイベントの数。                  |
| `links`                  | *array*  | 他スパンへのリンクの配列。                    |
| `droppedLinksCount`      | *number* | ドロップされたリンクの数。                    |
| `status`                 | *object* | スパンの実行状態。                            |
| `resource`               | *object* | リソース情報。                                |
| `scope`                  | *object* | スコープ情報。                                |

スパン属性オブジェクトは以下のキーを持ちます。

| KEY      | TYPE       | DESCRIPTION          |
| -------- | ------     | -----------          |
| `key`    | *string*   | 属性のキー           |
| `value`  | *AnyValue* | イベント名。         |

*AnyValue* は値の型を示す`valueType`と、値を示す`〜Value`で構成されます（`empty`のみ例外）。`valueType`キーの値に対応して、利用できる`〜Value` キーの名前および値は変わります。

| valueTypeの値 | KEY          | TYPE       |
| --------    | ------     | --------      |
| **string** | `stringValue` | *string*    |
| **bool**   | `boolValue`   | *bool*      |
| **int**    | `intValue`    | *int*       |
| **double** | `doubleValue` | *double*    |
| **array**  | `arrayValue`  | *array*     |
| **kvlist** | `kvlistValue` | *kvlist*    |
| **bytes**  | `bytesValue`  | *bytes*     |
| **empty**  | なし          | なし         |

*array* は他の値を内包しています。具体的には以下のような構造となります。

```json
{
  "valueType": "array",
  "arrayValue": [
    {"valueType": "int", "intValue": 10},
    {"valueType": "int", "intValue": 20}
  ]
}
```

*kvlist* は他の値を内包しています。具体的には以下のような構造となります。

```json
{
  "valueType": "kvlist",
  "kvlistValue": {
    "en": {"valueType": "string", "stringValue": "success"}
  }
}
```

スパンイベントオブジェクトは以下のキーを持ちます。

| KEY                      | TYPE     | DESCRIPTION                                     |
| --------                 | ------   | -----------                                     |
| `time`                   | *string* | イベントの発生時刻。`YYYY-mm-ddTHH:MM:SS.sss+Z` |
| `name`                   | *string* | イベント名。                                    |
| `attributes`             | *array*  | イベント属性の配列。                            |
| `droppedAttributesCount` | *number* | ドロップされた属性の数。                        |

他スパンへのリンクオブジェクトは以下のキーを持ちます。

| KEY                      | TYPE     | DESCRIPTION                    |
| --------                 | ------   | -----------                    |
| `traceId`                | *string* | リンク先のトレースID。         |
| `spanId`                 | *string* | リンク先のスパンID。           |
| `traceState`             | *string* | W3C Trace Contextのtracestate  |
| `attributes`             | *array*  | リンク属性の配列。             |
| `droppedAttributesCount` | *number* | ドロップされたリンク属性の数。 |

スパンの実行状態オブジェクトは以下のキーを持ちます。

| KEY       | TYPE     | DESCRIPTION                                |
| --------  | ------   | -----------                                |
| `message` | *string* | ステータスメッセージ。                     |
| `code`    | *string* | ステータスコード文字列(unset, ok, error)。 |

リソース情報オブジェクトは以下のキーを持ちます。

| KEY                      | TYPE     | DESCRIPTION                      |
| --------                 | ------   | -----------                      |
| `attributes`             | *array*  | リソース属性の配列。             |
| `droppedAttributesCount` | *number* | ドロップされたリソース属性の数。 |

スコープ情報オブジェクトは以下のキーを持ちます。

| KEY                      | TYPE     | DESCRIPTION                      |
| --------                 | ------   | -----------                      |
| `name`                   | *string* | スコープ名。                     |
| `version`                | *string* | スコープバージョン。             |
| `attributes`             | *array*  | スコープ属性の配列。             |
| `droppedAttributesCount` | *number* | ドロップされたスコープ属性の数。 |

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
      <td>無効なリクエストのとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>指定されたトレースにアクセスする権限がないとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのトレースがみつからないとき</td>
    </tr>
  </tbody>
</table>
