---
Title: APM
Date: 2025-11-04T00:00:00+09:00
URL: https://mackerel.io/ja/api-docs/entry/apm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/17179246901317393644
---

<ul class="internal-nav">
  <li><a href="#http-server-stats">HTTPサーバー統計情報の取得</a></li>
  <li><a href="#db-query-stats">データベースクエリ統計情報の取得</a></li>
</ul>

<h2 id="http-server-stats">HTTPサーバー統計情報の取得</h2>

トレーシング機能で投稿されたデータから、指定された条件でHTTPサーバーの統計情報を取得します。SERVER 側の HTTP リクエストのみを集計し、CLIENT スパンは除外されます。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/apm/http-server-stats</code>
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
| `method`           | *string*  | [optional] HTTPメソッドでフィルタリング。OpenTelemetryのsemantic conventionsの `http.request.method` もしくは `http.method` に対応します。`http.request.method` が優先されます。 |
| `route`            | *string*  | [optional] ルートでフィルタリング（部分一致）。OpenTelemetryのsemantic conventionsの `http.route`、`url.path`、`http.target`、`http.url` に対応します。この順で優先されます。 |
| `orderColumn`      | *string*  | [optional] ソート列。`SUM`（合計レイテンシー）、`AVERAGE`（平均レイテンシー）、`P95`（95パーセンタイルレイテンシー）、`ERROR_RATE`（エラー率）、`REQUEST_COUNT`（アクセス数）のいずれか。デフォルトは `P95`。 |
| `orderDirection`   | *string*  | [optional] ソート順。`ASC`（昇順）または `DESC`（降順）。デフォルトは `DESC`。                                             |
| `page`             | *number*  | [optional] ページ番号（1から始まる）。デフォルトは `1`。                                                                   |
| `perPage`          | *number*  | [optional] 1ページあたりの件数（1〜100）。デフォルトは `20`。                                                              |

### 応答

#### 成功時

```json
{
  "results": [
    {
      "method": "GET",
      "route": "/api/users",
      "totalMillis": 837.0,
      "averageMillis": 9.01,
      "approxP95Millis": 19.89,
      "errorRatePercentage": 0.0,
      "requestCount": 93
    },
    {
      "method": "POST",
      "route": "/api/orders",
      "totalMillis": 1250.0,
      "averageMillis": 12.5,
      "approxP95Millis": 25.5,
      "errorRatePercentage": 2.5,
      "requestCount": 100
    }
  ],
  "hasNextPage": false
}
```

レスポンスオブジェクトは以下のキーを持ちます。

| KEY           | TYPE      | DESCRIPTION                              |
| ------------- | --------- | ---------------------------------------- |
| `results`     | *array*   | HTTPサーバ統計情報の配列                 |
| `hasNextPage` | *boolean* | 次のページが存在するか                   |

HTTPサーバ統計情報オブジェクトは以下のキーを持ちます。

| KEY                    | TYPE     | DESCRIPTION                                 |
| ---------------------- | -------- | ------------------------------------------- |
| `method`               | *string* | HTTPメソッド。OpenTelemetryのsemantic conventionsの `http.request.method` もしくは `http.method` に対応します。`http.request.method` が優先されます。 |
| `route`                | *string* | HTTPルート。OpenTelemetryのsemantic conventionsの `http.route`、`url.path`、`http.target`、`http.url` に対応します。この順で優先されます。 |
| `totalMillis`          | *number* | 合計レイテンシー（ミリ秒）。Spanのduration（end - start）を計算した値 |
| `averageMillis`        | *number* | 平均レイテンシー（ミリ秒）。合計実行時間をアクセス数で割った平均値 |
| `approxP95Millis`      | *number* | おおよそのP95レイテンシー（ミリ秒）         |
| `errorRatePercentage`  | *number* | エラー率（%）。HTTPステータスコードが500以上のリクエストの割合 |
| `requestCount`         | *number* | アクセス数。該当するHTTPリクエストを処理したspanの数 |

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

<h2 id="db-query-stats">データベースクエリ統計情報の取得</h2>

トレーシング機能で投稿されたデータから、指定された条件でデータベースのクエリ統計情報を取得します。データベースへのアクセスを示す CLIENT スパンのみを集計し、SERVER スパンは除外されます。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/apm/db-query-stats</code>
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
| `query`            | *string*  | [optional] SQLクエリでフィルタリング（部分一致）。OpenTelemetryのsemantic conventionsの `db.query.text` もしくは `db.statement` に対応します。`db.query.text` が優先されます。 |
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
      "query": "INSERT INTO orders (user_id, total) VALUES (?, ?)",
      "executionCount": 850,
      "totalMillis": 6375.0,
      "averageMillis": 7.5,
      "approxP95Millis": 15.2
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

| KEY                    | TYPE     | DESCRIPTION                                 |
| ---------------------- | -------- | ------------------------------------------- |
| `query`                | *string* | SQLクエリ文字列。OpenTelemetryのsemantic conventionsの `db.query.text` もしくは `db.statement` に対応します。`db.query.text` が優先されます。 |
| `executionCount`       | *number* | 実行回数。該当するクエリを実行したspanの数 |
| `totalMillis`          | *number* | 合計レイテンシー（ミリ秒）。Spanのduration（end - start）を計算した値 |
| `averageMillis`        | *number* | 平均レイテンシー（ミリ秒）。合計実行時間を実行回数で割った平均値 |
| `approxP95Millis`      | *number* | おおよそのP95レイテンシー（ミリ秒）         |

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
