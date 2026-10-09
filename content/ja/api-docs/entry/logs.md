---
Title: ログ
Date: 2026-09-17T17:00:46+09:00
URL: https://mackerel.io/ja/api-docs/entry/logs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/14945776032078815895
---

<ul class="internal-nav">
  <li><a href="#list">ログ一覧の取得</a></li>
  <li><a href="#list-saved-searches">保存された検索条件の一覧の取得</a></li>
</ul>

<h2 id="list">ログ一覧の取得</h2>

指定された条件でログの一覧を取得します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/logs</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力

| KEY                | TYPE            | DESCRIPTION                                                                                                                       |
| ------------------ | --------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `serviceName`      | *string*        | サービス名                                                                                                                         |
| `serviceNamespace` | *string*        | [optional] サービスの名前空間。デフォルトは空文字列(`""`)                                                                         |
| `from`             | *number*        | ログ検索開始時刻（Unix epoch秒）。`from` 以上 `to` 未満の半開区間で検索します                                                      |
| `to`               | *number*        | ログ検索終了時刻（Unix epoch秒）。`from` より前の時刻は指定できません。また `from` から `to` までの期間は最大30日です              |
| `keywords`         | *array[string]* | [optional] 本文の絞り込みキーワードのリスト。本文への部分一致で判定し、複数指定した場合はすべてを含むログを返します。省略した場合は本文で絞り込みません。空配列・空文字列は指定できません |
| `severities`       | *array[string]* | [optional] ログレベルの絞り込み条件のリスト。ログレベルは `UNSPECIFIED`, `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL` のいずれかを指定でき、複数指定した場合はいずれかに一致するログを返します |
| `traceId`          | *string*        | [optional] トレースIDによる絞り込み（16進数文字列32桁）                                                                           |
| `bodyAttributes`   | *array[object]* | [optional] 構造化ログのフィールドによる絞り込み条件のリスト。複数指定した場合はすべてを満たすログを返します                      |
| `order`            | *object*        | [optional] ソート条件                                                                                                             |
| `pageCursor`       | *object*        | [optional] ページングの起点。指定しない場合は `order` で指定した並び順の先頭から取得します                                        |
| `perPage`          | *number*        | [optional] 1ページあたりの件数（1〜1000）。デフォルトは50                                                                          |

`severities` に指定するログレベルは、[OpenTelemetryのSeverityNumber](https://opentelemetry.io/docs/specs/otel/logs/data-model/#field-severitynumber)に対応する文字列です。

構造化ログのフィールドによる絞り込み条件オブジェクトは以下のキーを持ちます。本文がJSONでない場合や、`keyPath` が指すフィールドが存在しない場合は一致しません。

| KEY        | TYPE            | DESCRIPTION                                                                                     |
| ---------- | --------------- | ----------------------------------------------------------------------------------------------- |
| `keyPath`  | *array[string]* | 絞り込むキーを指すパス。ネストの順にキーを並べます                                              |
| `value`    | *string*        | フィールドの値（文字列として指定）                                                              |
| `operator` | *string*        | 比較演算子。 `EQ`, `NEQ`, `GT`, `GTE`, `LT`, `LTE`, `STARTS_WITH` のいずれかを指定できます       |
| `type`     | *string*        | フィールド値の型。 `string`, `int`, `double`, `bool` のいずれかを指定できます                   |

`keyPath` は、ネストしたキー（`{"req": {"method": ...}}`）であれば `["req", "method"]` のように指定します。

フィールド値の `type` によって利用可能な `operator` が異なります。

| operator      | string | int | double | bool |
| ------------- | ------ | --- | ------ | ---- |
| `EQ`          | ○      | ○   | ○      | ○    |
| `NEQ`         | ○      | ×   | ×      | ○    |
| `GT`          | ×      | ○   | ○      | ×    |
| `GTE`         | ×      | ○   | ○      | ×    |
| `LT`          | ×      | ○   | ○      | ×    |
| `LTE`         | ×      | ○   | ○      | ×    |
| `STARTS_WITH` | ○      | ×   | ×      | ×    |

`NEQ` は、フィールドを持つログのうち値が一致しないものを返します。フィールドを持たないログは一致しません。

ソート条件オブジェクトは以下のキーを持ちます。

| KEY         | TYPE     | DESCRIPTION                                              |
| ----------- | -------- | ---------------------------------------------------------- |
| `column`    | *string* | [optional] ソート列は `TIMESTAMP` を指定できます。デフォルトは `TIMESTAMP` |
| `direction` | *string* | [optional] ソート順は `ASC`, `DESC` のいずれかを指定できます。デフォルトは `DESC`   |

ページングの起点オブジェクトは以下のキーを持ちます。

| KEY         | TYPE     | DESCRIPTION                                                                                                     |
| ----------- | -------- | ----------------------------------------------------------------------------------------------------------------- |
| `direction` | *string* | ページングの方向は `NEXT`, `PREVIOUS` のいずれかを指定できます。`order` で指定した並び順における、`cursor` から見たページングの方向で、`NEXT` は `cursor` より後（`order` の並びで後ろ）のログ、`PREVIOUS` は `cursor` より前のログを取得します |
| `cursor`    | *string* | 起点となるカーソル。`pagination.startCursor` または `pagination.endCursor` の値を指定します。起点に指定したログ自体は結果に含まれず、`direction` の方向で次のログから返します |

#### 入力例

```json
{
  "serviceName": "shoppingcart",
  "serviceNamespace": "shop",
  "from": 1788220800,
  "to": 1788739200,
  "keywords": ["error"],
  "severities": ["ERROR", "WARN"],
  "traceId": "550e8400e29b41d4a716446655440000",
  "bodyAttributes": [
    {
      "keyPath": ["status_code"],
      "value": "500",
      "operator": "EQ",
      "type": "int"
    },
    {
      "keyPath": ["req", "method"],
      "value": "GET",
      "operator": "EQ",
      "type": "string"
    },
    {
      "keyPath": ["http.target"],
      "value": "/api/v0",
      "operator": "STARTS_WITH",
      "type": "string"
    }
  ],
  "order": {
    "column": "TIMESTAMP",
    "direction": "DESC"
  },
  "pageCursor": {
    "direction": "NEXT",
    "cursor": "cursor-string"
  },
  "perPage": 100
}
```

### 応答

#### 成功時

```json
{
  "results": [
    {
      "cursor": "cursor-string",
      "timestamp": "2026-09-07T12:34:56.789123456Z",
      "severity": "ERROR",
      "severityNumber": 17,
      "body": "request failed",
      "traceId": "550e8400e29b41d4a716446655440000",
      "spanId": "051581bf3cb55c13",
      "serviceName": "shoppingcart",
      "serviceNamespace": "shop",
      "attributes": [
        {
          "key": "http.status_code",
          "value": {
            "valueType": "string",
            "stringValue": "500"
          }
        }
      ],
      "resourceAttributes": [
        {
          "key": "host.name",
          "value": {
            "valueType": "string",
            "stringValue": "server"
          }
        }
      ],
      "scopeAttributes": []
    }
  ],
  "pagination": {
    "hasNextPage": true,
    "hasPreviousPage": false,
    "startCursor": "cursor-string",
    "endCursor": "cursor-string"
  }
}
```

レスポンスは以下のキーを持ちます。

| KEY          | TYPE     | DESCRIPTION                        |
| ------------ | -------- | ---------------------------------- |
| `results`    | *array*  | ログレコードのリスト               |
| `pagination` | *object* | カーソル方式のページネーション情報 |

ログレコードのオブジェクトは以下のキーを持ちます。

| KEY                  | TYPE     | DESCRIPTION                                                         |
| -------------------- | -------- | --------------------------------------------------------------------- |
| `cursor`             | *string* | このログを指すカーソル                                              |
| `timestamp`          | *string* | ログの時刻（RFC 3339形式）                                          |
| `severity`           | *string* | ログレベル                                                          |
| `severityNumber`     | *number* | OpenTelemetryのSeverityNumber                                       |
| `body`               | *string* | ログ本文                                                            |
| `traceId`            | *string* | 関連するトレースID（16進数文字列32桁）。関連付けがない場合は`null` |
| `spanId`             | *string* | 関連するスパンID（16進数文字列16桁）。関連付けがない場合は`null`   |
| `serviceName`        | *string* | サービス名                                                          |
| `serviceNamespace`   | *string* | サービスの名前空間。設定されていない場合は`null`                    |
| `attributes`         | *array*  | ログレコード属性のリスト                                            |
| `resourceAttributes` | *array*  | リソース属性のリスト                                                |
| `scopeAttributes`    | *array*  | スコープ属性のリスト                                                |

属性オブジェクトは以下のキーを持ちます。

| KEY     | TYPE     | DESCRIPTION |
| ------- | -------- | ----------- |
| `key`   | *string* | 属性のキー  |
| `value` | *object* | 属性の値    |

`value` は、値の型を示す `valueType` と、その型に対応する値のキー（`〜Value`）で構成されるオブジェクトです。現在 `valueType` は `string` のみで、値は `stringValue` に文字列として入ります。

```json
{
  "valueType": "string",
  "stringValue": "500"
}
```

ページネーション情報オブジェクトは以下のキーを持ちます。

| KEY               | TYPE      | DESCRIPTION                                                                             |
| ----------------- | --------- | ----------------------------------------------------------------------------------------- |
| `hasNextPage`     | *boolean* | 次のページ（`pageCursor.direction` が `NEXT` のページ）が存在するか                       |
| `hasPreviousPage` | *boolean* | 前のページ（`pageCursor.direction` が `PREVIOUS` のページ）が存在するか                   |
| `startCursor`     | *string*  | 結果先頭のカーソル。`pageCursor.cursor` に指定して `PREVIOUS` 方向にページングできます。結果が空の場合は`null` |
| `endCursor`       | *string*  | 結果末尾のカーソル。`pageCursor.cursor` に指定して `NEXT` 方向にページングできます。結果が空の場合は`null`     |

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
      <td>オーガニゼーションでログ機能を利用できないとき / ログへのアクセスが許可されていないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>429</td>
      <td>レート制限を超過したとき（1秒あたり1リクエスト）。再試行までの待ち時間を秒数で表した <code>Retry-After</code> ヘッダーを返します</td>
    </tr>
  </tbody>
</table>

<h2 id="list-saved-searches">保存された検索条件の一覧の取得</h2>

保存された検索条件の一覧を取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/saved-log-searches</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "results": [
    {
      "id": "1234",
      "name": "エラーログ確認用",
      "description": null,
      "definition": {
        "serviceName": "shoppingcart",
        "serviceNamespace": "shop",
        "keywords": ["timeout"],
        "severities": ["ERROR", "FATAL"],
        "traceId": null,
        "bodyAttributes": [
          {
            "keyPath": ["status_code"],
            "value": "500",
            "operator": "GTE",
            "type": "int"
          }
        ]
      },
      "createdAt": 1718802000,
      "updatedAt": 1718888400
    }
  ]
}
```

レスポンスは以下のキーを持ちます。

| KEY       | TYPE    | DESCRIPTION                      |
| --------- | ------- | -------------------------------- |
| `results` | *array* | 保存された検索条件のリスト |

保存された検索条件のオブジェクトは以下のキーを持ちます。

| KEY           | TYPE     | DESCRIPTION                        |
| ------------- | -------- | ---------------------------------- |
| `id`          | *string* | 保存された検索条件のID             |
| `name`        | *string* | 名前                               |
| `description` | *string* | 説明。設定されていない場合は`null` |
| `definition`  | *object* | 内容                               |
| `createdAt`   | *number* | 作成した時刻（Unix epoch秒）       |
| `updatedAt`   | *number* | 最後に変更した時刻（Unix epoch秒） |

検索条件の内容のオブジェクトは以下のキーを持ちます。各キーは[ログ一覧の取得](#list)の同名の入力パラメータに対応します。

| KEY                | TYPE            | DESCRIPTION                                            |
| ------------------ | --------------- | ------------------------------------------------------ |
| `serviceName`      | *string*        | サービス名                                             |
| `serviceNamespace` | *string*        | サービスの名前空間。設定されていない場合は`null`       |
| `keywords`         | *array[string]* | 本文の絞り込みキーワードのリスト                       |
| `severities`       | *array[string]* | ログレベルの絞り込み条件のリスト                       |
| `traceId`          | *string*        | トレースIDによる絞り込み。設定されていない場合は`null` |
| `bodyAttributes`   | *array[object]* | 構造化ログのフィールドによる絞り込み条件のリスト       |

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
      <td>401</td>
      <td>APIキーが無効なとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>オーガニゼーションでログ機能を利用できないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
