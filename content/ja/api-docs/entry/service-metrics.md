---
Title: サービスメトリック
Date: 2016-03-22T10:33:51+09:00
URL: https://mackerel.io/ja/api-docs/entry/service-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368064365
---

<ul class="internal-nav">
  <li><a href="#post">サービスメトリックの投稿</a></li>
  <li><a href="#get">サービスメトリックの値の取得</a></li>
</ul>

<h2 id="post">サービスメトリックの投稿</h2>

サービスに紐づくメトリック（計測値）を Mackerel に送信します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/tsdb</code>
</p>

APIに対して過去の値を送信した場合、Mackerel上の値は上書きされます。24時間以上前の古いタイムスタンプで投稿した場合は、そのメトリックは記録されません（ステータスコード 200 でレスポンスを返します）。また、アラートの発生はMackerel上の最新の値に基づいて行われるため、そのデータが最新の値ではない場合には、閾値を超えていたとしてもアラートは発生しません。

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

```json
[ <metricValue>, <metricValue>, … ]
```

<i>`<metricValue>`</i>: 以下のキーをもつオブジェクトです。

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name`   | *string* | メトリック名（`[a-zA-Z0-9._-]+`） |
| `time`   | *number* | エポック秒。 |
| `value`  | *number* | `time` 時点での計測値。 |

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
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>429</td>
      <td>分間のリクエスト数が過剰のとき。投稿頻度を1分間隔にする、複数のメトリックをまとめて投稿するなどの修正をお願いします。</td>
    </tr>
    <tr>
      <td>200以外</td>
      <td>上記以外のエラー</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">サービスメトリックの値の取得</h2>

サービスに紐づくメトリックの値を取得できます。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metrics</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 入力（クエリパラメータ）

次のパラメータでメトリック名と取得する期間を指定します。

| KEY     | TYPE     | DESCRIPTION |
| ------- | -------- | ----------- |
| `name`  | *string* | メトリック名。 |
| `from`  | *number* | メトリックを取得したい期間の最初 (epoch秒) |
| `to`    | *number* | メトリックを取得したい期間の最後 (epoch秒) |

### 応答

#### 成功時

```json
{
  "metrics": [
    {
      "time": <time>,
      "value": <value>
    },
    {
      "time": <time>,
      "value": <value>
    },
    …
  ]
}
```

- 並び順は、時刻が古い順です
- メトリックの時刻を表す <i>`<time>`</i> はエポック秒です

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
      <td>サービスまたはメトリックが存在しない時</td>
    </tr>
    <tr>
      <td>200以外</td>
      <td>上記以外のエラー</td>
    </tr>
  </tbody>
</table>


<h2 id="delete-graph-def">サービスメトリックのグラフ定義の削除</h2>

サービスメトリックのグラフ定義を削除します。サービスメトリックを送ると、再度グラフが生成されます。

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/graph-defs/<em>&lt;graphName&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

| KEY     | TYPE     | DESCRIPTION |
| ------- | -------- | ----------- |
| `serviceName`  | *string* | サービス名 |
| `graphName`  | *string* | 削除するグラフの名前（`*`で終わるもの） |

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
      <td>サービスもしくはグラフ定義が存在しないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>
