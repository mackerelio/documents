---
Title: データベースクエリ統計情報
Date: 2025-11-04T00:00:00+09:00
URL: https://mackerel.io/ja/api-docs/entry/db-query-stats
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/17179246901316687757
---

<ul class="internal-nav">
  <li><a href="#list">データベースクエリ統計情報の取得</a></li>
</ul>

<h2 id="list">データベースクエリ統計情報の取得</h2>

トレーシング機能で投稿されたデータから、指定した条件に一致するデータベースのクエリ統計情報を取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/db-query-stats</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力

このAPIはクエリパラメータで条件を指定します。以下のパラメータが利用できます。

| PARAM NAME         | TYPE      | DESCRIPTION                                                                                                                |
| ------------------ | --------- | -------------------------------------------------------------------------------------------------------------------------- |
| `serviceName`      | *string*  | [required] サービス名。OpenTelemetryのsemantic conventionsの `service.name` に対応します。                                 |
| `serviceNamespace` | *string*  | [optional] サービスの名前空間。OpenTelemetryのsemantic conventionsの `service.namespace` に対応します。                    |
| `from`             | *number*  | [required] 統計情報取得開始時刻（Unix epoch秒）。`to` との範囲は30分に制限されます。                                        |
| `to`               | *number*  | [required] 統計情報取得終了時刻（Unix epoch秒）。`from` との範囲は30分に制限されます。                                      |
| `environment`      | *string*  | [optional] 環境名。OpenTelemetryのsemantic conventionsの `deployment.environment` もしくは `deployment.environment.name` に対応します。 |
| `version`          | *string*  | [optional] バージョン。OpenTelemetryのsemantic conventionsの `service.version` に対応します。                               |
| `query`            | *string*  | [optional] SQLクエリでフィルタリング（部分一致）。OpenTelemetryのsemantic conventionsの `db.query.text` もしくは `db.statement` に対応します。新しい仕様である `db.query.text` が優先されます。 |
| `orderColumn`      | *string*  | [optional] ソート列。`SUM`（合計レイテンシー）、`AVERAGE`（平均レイテンシー）、`P95`（95パーセンタイルレイテンシー）、`EXECUTION_COUNT`（実行回数）のいずれか。デフォルトは `P95`。 |
| `orderDirection`   | *string*  | [optional] ソート順。`ASC`（昇順）または `DESC`（降順）。デフォルトは `DESC`。                                             |
| `page`             | *number*  | [optional] ページ番号（1から始まる）。デフォルトは `1`。                                                                   |
| `perPage`          | *number*  | [optional] 1ページあたりの件数（1〜100）。デフォルトは `20`。                                                              |

### 応答

#### 成功時

```json
{
  "results": [
    {
      "query": "SELECT * FROM users WHERE id = ?",
      "executionCount": 1250,
      "totalMillis": 11262.5,
      "averageMillis": 9.01,
      "approxP95Millis": 19.89
    },
    {
      "query": "INSERT INTO orders (user_id, product_id) VALUES (?, ?)",
      "executionCount": 450,
      "totalMillis": 4500.0,
      "averageMillis": 10.0,
      "approxP95Millis": 25.5
    }
  ],
  "hasNextPage": false
}
```

レスポンスオブジェクトは以下のキーを持ちます。

| KEY           | TYPE      | DESCRIPTION                              |
| ------------- | --------- | ---------------------------------------- |
| `results`     | *array*   | データベースクエリ統計情報の配列         |
| `hasNextPage` | *boolean* | 次のページが存在するか                   |

データベースクエリ統計情報オブジェクトは以下のキーを持ちます。

| KEY               | TYPE     | DESCRIPTION                                 |
| ----------------- | -------- | ------------------------------------------- |
| `query`           | *string* | SQLクエリ文字列。OpenTelemetryのsemantic conventionsの `db.query.text` または `db.statement` に対応します。 |
| `executionCount`  | *number* | 該当するクエリを実行したspanの数            |
| `totalMillis`     | *number* | 合計レイテンシー（ミリ秒）。Spanのduration（end - start）を計算した値 |
| `averageMillis`   | *number* | 平均レイテンシー（ミリ秒）。合計実行時間を実行回数で割った平均値 |
| `approxP95Millis` | *number* | おおよそのP95レイテンシー（ミリ秒）         |

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
      <td>入力が受け付けられない形式のとき</td>
    </tr>
    <tr>
      <td>401</td>
      <td>APIキーが無効のとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>指定されたサービスにアクセスする権限がないとき</td>
    </tr>
    <tr>
      <td>429</td>
      <td>APIのリクエストレート制限を超えているとき（1 req/sec）</td>
    </tr>
  </tbody>
</table>
