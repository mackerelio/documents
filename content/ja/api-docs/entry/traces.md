---
Title: トレース
Date: 2025-07-24T12:50:00+09:00
URL: https://mackerel.io/ja/api-docs/entry/traces
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/6802418398526653045
---

<ul class="internal-nav">
  <li><a href="#get">トレースの取得</a></li>
</ul>

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
