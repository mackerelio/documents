---
Title: ダッシュボード
Date: 2016-03-22T10:45:43+09:00
URL: https://mackerel.io/ja/api-docs/entry/dashboards
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368066406
---

<ul class="internal-nav">
  <li><a href="#create">ダッシュボードの作成</a></li>
  <li><a href="#get">ダッシュボードの取得</a></li>
  <li><a href="#update">ダッシュボードの更新</a></li>
  <li><a href="#delete">ダッシュボードの削除</a></li>
  <li><a href="#list">ダッシュボードの一覧</a></li>
</ul>

<h2 id="create">ダッシュボードの作成</h2>

ダッシュボードを作成します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/dashboards</code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力
以下のキーをもつオブジェクト。

| KEY       | TYPE            | DESCRIPTION                                       |
| --------- | --------------- | ------------------------------------------------- |
| `title`   | *string*        | ダッシュボード名                                  |
| `memo`    | *string*        | ダッシュボードのメモ                              |
| `urlPath` | *string*        | ダッシュボードのURLのパス \*1                     |
| `widgets` | *array[object]* | [ウィジェットを表すオブジェクト](#widget)のリスト |

\*1 `urlPath` のフォーマットは以下の通りです:

`^([A-Za-z0-9_][-A-Za-z0-9_]*)(/[A-Za-z0-9_][-A-Za-z0-9_]*)*$`

### 応答
#### 成功時
作成されたダッシュボードを返します。

| KEY         | TYPE            | DESCRIPTION                                       |
| ----------- | --------------- | ------------------------------------------------- |
| `id`        | *string*        | ダッシュボードのID \*1                            |
| `title`     | *string*        | ダッシュボード名                                  |
| `memo`      | *string*        | ダッシュボードのメモ                              |
| `urlPath`   | *string*        | ダッシュボードのURLのパス                         |
| `widgets`   | *array[object]* | [ウィジェットを表すオブジェクト](#widget)のリスト |
| `createdAt` | *number*        | 作成時刻（エポック秒）                            |
| `updatedAt` | *number*        | 最終更新時刻（エポック秒）                        |

\*1 `id` は新しく作成されたダッシュボードに割り当てられたIDです。以降のリクエストではこのIDを用いてダッシュボードを識別します。

### 失敗時
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
      <td>403</td>
      <td>作成できるダッシュボード数の上限に達しているとき / APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>409</td>
      <td><code>urlPath</code> に指定したURLのパスに既に他のダッシュボードが存在しているとき</td>
    </tr>
  </tbody>
</table>


----------------------------------------------

<h2 id="get">ダッシュボードの取得</h2>
<p class="type-get">
  <code>GET</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答
#### 成功時
[ダッシュボードの作成](#create)と同様の形式です。

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
      <td>指定されたIDのダッシュボードがみつからないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">ダッシュボードの更新</h2>
<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力
[ダッシュボードの作成](#create)と同様の形式です。

### 応答
#### 成功時
更新後のダッシュボードを[ダッシュボードの作成](#create)と同様の形式で返します。

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
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのダッシュボードがみつからないとき</td>
    </tr>
    <tr>
      <td>409</td>
      <td><code>urlPath</code> に指定したURLのパスに既に他のダッシュボードが存在しているとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">ダッシュボードの削除</h2>

指定されたIDのダッシュボードを削除します。

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答
#### 成功時
削除する直前のダッシュボードを[ダッシュボードの作成](#create)と同様の形式で返します。

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
      <td>指定されたIDのダッシュボードがみつからないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="list">ダッシュボードの一覧</h2>
<p class="type-get">
  <code>GET</code>
  <code>/api/v0/dashboards</code>
</p>

### APIキーに必要な権限
<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答
| KEY          | TYPE            | DESCRIPTION            |
| ------------ | --------------- | ---------------------- |
| `dashboards` | *array[object]* | ダッシュボードのリスト |

それぞれのダッシュボードを[ダッシュボードの作成](#create)と同様の形式で返します。

----------------------------------------------

<h2 id="widget">ウィジェット</h2>
ウィジェットを表すオブジェクトは、各ウィジェットの種類ごとに、以下の形式となっています。

### グラフウィジェット
| KEY      | TYPE     | DESCRIPTION                                                   |
| -------- | -------- | ------------------------------------------------------------- |
| `type`   | *string* | 固定文字列 `"graph"`                                          |
| `title`  | *string* | ウィジェットのタイトル                                        |
| `graph`  | *object* | [グラフを表すオブジェクト](#graph)                            |
| `range`  | *object* | [optional] [グラフの表示期間を表すオブジェクト](#graph-range) |
| `referenceLines` | *array[object]* | [optional] [補助線を表すオブジェクト](#reference-line)。補助線の設定を削除したい場合には、空の配列を指定してください。<br />ダッシュボードの取得時に補助線が未設定の場合、空の配列が返されます。配列に2つ以上の要素を指定できません。 |
| `layout` | *object* | [レイアウトを表すオブジェクト](#layout)                       |

### 数値ウィジェット
| KEY            | TYPE     | DESCRIPTION                                |
| -------------- | -------- | ------------------------------------------ |
| `type`         | *string* | 固定文字列 `"value"`                       |
| `title`        | *string* | ウィジェットのタイトル                     |
| `metric`       | *object* | [メトリックを表すオブジェクト](#metric)    |
| `fractionSize` | *number* | [optional] 表示する小数点以下の桁数 (0–16) |
| `suffix`       | *string* | [optional] 数値の後に表示する単位 |
| `formatRules` | *array[object]* | [optional] [フォーマットルールを表すオブジェクト](#format-rule)。フォーマットルールの設定を削除したい場合には、空の配列を指定してください。<br />ダッシュボードの取得時にフォーマットルールが未設定の場合、空の配列が返されます。配列に2つ以上の要素を指定できません。 |
| `layout`       | *object* | [レイアウトを表すオブジェクト](#layout)    

### Markdownウィジェット
| KEY        | TYPE     | DESCRIPTION                             |
| ---------- | -------- | --------------------------------------- |
| `type`     | *string* | 固定文字列 `"markdown"`                 |
| `title`    | *string* | ウィジェットのタイトル                  |
| `markdown` | *string* | Markdown形式の文字列                    |
| `layout`   | *object* | [レイアウトを表すオブジェクト](#layout) |

### アラートステータスウィジェット
| KEY        | TYPE     | DESCRIPTION                             |
| ---------- | -------- | --------------------------------------- |
| `type`     | *string* | 固定文字列 `"alertStatus"`                 |
| `title`    | *string* | ウィジェットのタイトル                  |
| `roleFullname` | *string*  | サービス名とロール名を`:`で連結したもの<br />ただし、ダッシュボード取得時に関連するロールやサービスが削除されていた場合、`roleFullname` には `null` が設定されます。 |
| `layout`   | *object* | [レイアウトを表すオブジェクト](#layout) |

<h3 id="graph">グラフ</h3>
#### ホストグラフ
| KEY      | TYPE     | DESCRIPTION                  |
| -------- | -------- | ---------------------------- |
| `type`   | *string* | 固定文字列 `"host"`          |
| `hostId` | *string* | ホストのID                   |
| `name`   | *string* | グラフ名（`"loadavg"` など） |

#### ロールグラフ
| KEY            | TYPE      | DESCRIPTION                                                                                     |
| -------------- | --------- | ----------------------------------------------------------------------------------------------- |
| `type`         | *string*  | 固定文字列 `"role"`                                                                             |
| `roleFullname` | *string*  | サービス名とロール名を`:`で連結したもの                                                         |
| `name`         | *string*  | グラフ名（`"loadavg5"` など）                                                                   |
| `isStacked`    | *boolean* | [optional] グラフが積み重ねグラフ / 線グラフのどちらか。`true` の場合、積み重ねグラフになります |

#### サービスグラフ
| KEY           | TYPE     | DESCRIPTION            |
| ------------- | -------- | ---------------------- |
| `type`        | *string* | 固定文字列 `"service"` |
| `serviceName` | *string* | サービス名             |
| `name`        | *string* | グラフ名               |

#### 式グラフ
| KEY          | TYPE     | DESCRIPTION               |
| ------------ | -------- | ------------------------- |
| `type`       | *string* | 固定文字列 `"expression"` |
| `expression` | *string* | グラフを表す式            |

これらに加えて、ダッシュボード取得時に関連するロール・サービス・グラフ定義が削除されていた場合、次の結果が返却されることがあります。

| KEY    | TYPE     | DESCRIPTION            |
| ------ | -------- | ---------------------- |
| `type` | *string* | 固定文字列 `"unknown"` |

<h3 id="graph-range">グラフの表示期間</h3>
未指定の場合は可変となり、ダッシュボード上部に表示されるコントローラーから表示期間を変更できるようになります。

#### 相対
| KEY      | TYPE     | DESCRIPTION             |
| -------- | -------- | ----------------------- |
| `type`   | *string* | 固定文字列 `"relative"` |
| `period` | *number* | 期間の長さ（秒）        |
| `offset` | *number* | 現在時刻からの差（秒）  |

（現在時刻 + `offset` - `period`）から（現在時刻 + `offset`）までの期間が表示されます。
`offset` に負の値を指定することで、過去の指定期間のグラフを表示できます。

#### 絶対
| KEY     | TYPE     | DESCRIPTION             |
| ------- | -------- | ----------------------- |
| `type`  | *string* | 固定文字列 `"absolute"` |
| `start` | *number* | 開始時刻（エポック秒）  |
| `end`   | *number* | 終了時刻（エポック秒）  |

`start` から `end` までの期間が表示されます。

<h3 id="reference-line">グラフの補助線</h3>
グラフの補助線を指定した場合、指定した値を基準として水平線が描画されます。

| KEY      | TYPE     | DESCRIPTION             |
| -------- | -------- | ----------------------- |
| `label`  | *string* | 補助線のラベル            |
| `value`  | *number* | 補助線の値                |

#### ラベル名について

ラベル名は下記の条件に従う必要があります。

- 空白文字を除いて1文字以上であること
- 32文字以下であること

#### 値について

値は下記の条件に従う必要があります。

- 0以上であること

<h3 id="format-rule">フォーマットルール</h3>
値のフォーマットルールを指定した場合、設定した条件式を満たす場合に強調したスタイルに変更されます。
例えば、`threshold` に `10` を指定し、`operator` に `">"` を指定した場合、数値が `10` より大きい場合に強調したスタイルに変更されます。

| KEY      | TYPE     | DESCRIPTION             |
| -------- | -------- | ----------------------- |
| `name`  | *string* | [optional] フォーマットルールの名前 |
| `threshold`  | *number* | 条件の基準となる値 |
| `operator`  | *string* | 強調したスタイルが適用される条件を指定。`">"` または `"<"`。 |

#### 名前について

名前は下記の条件に従う必要があります。

- 32文字以下であること

<h3 id="metric">メトリック</h3>
#### ホストメトリック

| KEY      | TYPE     | DESCRIPTION                       |
| -------- | -------- | --------------------------------- |
| `type`   | *string* | 固定文字列 `"host"`               |
| `hostId` | *string* | ホストのID                        |
| `name`   | *string* | メトリック名（`"loadavg5"` など） |

#### サービスメトリック

| KEY           | TYPE     | DESCRIPTION            |
| ------------- | -------- | ---------------------- |
| `type`        | *string* | 固定文字列 `"service"` |
| `serviceName` | *string* | サービス名             |
| `name`        | *string* | メトリック名           |

#### 式

| KEY          | TYPE     | DESCRIPTION               |
| ------------ | -------- | ------------------------- |
| `type`       | *string* | 固定文字列 `"expression"` |
| `expression` | *string* | メトリックを表す式        |

式によるメトリック指定を使用して数値を正しく表示するためには、式の結果が単一の系列を表すようにする必要があります。

これらに加えて、ダッシュボード取得時に関連するサービスが削除されていた場合、次の結果が返却されることがあります。

| KEY    | TYPE     | DESCRIPTION            |
| ------ | -------- | ---------------------- |
| `type` | *string* | 固定文字列 `"unknown"` |

<h3 id="layout">レイアウト</h3>
| KEY      | TYPE     | DESCRIPTION         |
| -------- | -------- | ------------------- |
| `x`      | *number* | ウィジェットのx座標 |
| `y`      | *number* | ウィジェットのy座標 |
| `width`  | *number* | ウィジェットの幅    |
| `height` | *number* | ウィジェットの高さ  |

座標はウィジェット表示領域の左上を原点（`x` = 0, `y` = 0）として、右方向をx軸、下方向をy軸の正の方向として指定します。また、各数値は以下の条件に従う必要があります。条件に従っていない場合、ウィジェットが期待した位置に表示されないことがあります。

- 各数値が0または正の整数であること
- ウィジェット表示領域 (幅24) からウィジェットがはみ出さないこと
- 他のウィジェットと重ならないこと
- 各ウィジェットの種類ごとに、以下の最小サイズ以上かつ最大サイズ以下であること

| ウィジェット | `width` の最小値 | `height` の最小値 | `width` の最大値 | `height` の最大値 |
| ------------ | ---------------- | ----------------- | ---------------- | ----------------- |
| グラフ       | 6                | 6                 | 24               | 32                |
| 数値         | 4                | 4                 | 24               | 32                |
| Markdown     | 4                | 2                 | 24               | 80                |
| アラートステータス | 4             | 3                 | 24               | 32                |
