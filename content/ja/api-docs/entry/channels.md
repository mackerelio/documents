---
Title: 通知チャンネル
Date: 2017-10-02T16:06:37+09:00
URL: https://mackerel.io/ja/api-docs/entry/channels
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/8599973812303795484
---

<ul class="internal-nav">
  <li><a href="#get">通知チャンネルの一覧取得</a></li>
  <li><a href="#create">通知チャンネルの登録</a></li>
  <li><a href="#update">通知チャンネルの更新</a></li>
  <li><a href="#delete">通知チャンネルの削除</a></li>
</ul>

<h2 id="get">通知チャンネルの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/channels</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "channels": [<channel>, <channel>, ...]
}
```

`<channel>` は以下のキーを持つオブジェクト

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `id`            | *string* | 通知チャンネルのID               |
| `name`          | *string* | 通知チャンネルの名称             |
| `type`          | *string* | 通知チャンネルの種類 (`"email"`, `"slack"`, `"line"`, `"chatwork"`, `"typetalk"`, `"twilio"`, `"pagerduty"`, `"opsgenie"`, `"jira-service-management"`, `"microsoft-teams`, `"webhook"`, `"amazon-event-bridge"` のいずれかです) |
| `suspendedAt`   | *number* / *null* | 通知チャンネルがサスペンドされた日時 (epoch秒)<br />サスペンドされていない場合は`null` |

`type` が `"email"`, `"slack"` または `"webhook"` の場合は上記に加えて、以下のように詳細な情報を返します

##### `type` が `email` の場合

| KEY             | TYPE            | DESCRIPTION                      |
| ------------    | -------------   | --------------------------------------------- |
| `emails`        | *array[string]* | 通知先に指定されているEmailアドレス           |
| `userIds`       | *array[string]* | 通知先に指定されているユーザーID                |
| `events`        | *array[string]*        | 通知するイベント(`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, `"monitor"` のいずれか) |

##### `type` が `slack` の場合

| KEY                 | TYPE            | DESCRIPTION                        |
| ------------        | -------------   | ---------------------------------- |
| `url`               | *string*        |  SlackのIncoming WebhookのURL      |
| `mentions`          | *hash[string]*  | キーは状態(`ok`, `warning`, `critical`のいずれか)、値はアラート通知の際に添えるテキストです  |
| `enabledGraphImage` | *boolean*       | 関連するグラフをSlackに投稿するか否か |
| `events`            | *array[string]*        | 通知するイベント(`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, `"monitor"` のいずれか) |

##### `type` が `webhook` の場合

| KEY                 | TYPE            | DESCRIPTION                        |
| ------------        | -------------   | ---------------------------------- |
| `url`               | *string*        |  HTTPリクエストを受け取るURL      |
| `enabledGraphImage` | *boolean*       | 関連するグラフのURLを通知内容に含めるか否か |
| `events`            | *array[string]*        | 通知するイベント(`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, `"monitor"` のいずれか) |

----------------------------------------------

<h2 id="create">通知チャンネルの登録</h2>

※現在はメール通知とSlack, Webhook の3つに対応しています

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/channels</code>
</p>

### APIキーに必要な情報

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

<ul class="internal-nav create">
  <li><a href="#create-email-channel">メール通知</a></li>
  <li><a href="#create-slack-channel">Slack</a></li>
  <li><a href="#create-webhook-channel">Webhook</a></li>
</ul>

<h3 id="create-email-channel">メール通知</h3>

#### 入力(メール通知)
| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `type`   | *string* | 固定文字列`"email"` |
| `name`   | *string* | チャンネルの名前 |
| `emails`        | *array[string]* | 通知先に指定するEmailアドレス           |
| `userIds`       | *array[string]* | 通知先に指定するユーザーID                |
| `events`        | *array[string]*        | 通知するイベント(`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, `"monitor"` のいずれか) |

#### 応答(メール通知)

##### 成功時

```json
{
  "id": <channelId>,
  "type": "email",
  "name": "My Channel",
  "emails": ["myaddress@example.com"],
  "userIds": ["userId"],
  "events": ["alert"]
}
```

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
      <td><code>emails</code>内のアドレスが不正な場合</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>userIds</code>内にオーガニゼーションに所属していないユーザーが含まれている場合</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>events</code>に上記以外のイベントが含まれる場合</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h3 id="create-slack-channel">Slack</h3>

#### 入力(Slack)

以下のキーをもつオブジェクト

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `type`   | *string* | 固定文字列`"slack"` |
| `name`   | *string* | チャンネルの名前 |
| `url`               | *string*        |  SlackのIncoming WebhookのURL      |
| `mentions`          | *hash[string]*  | キーは状態(`ok`, `warning`, `critical`のいずれか)、値はアラート通知の際に添えるテキストです  |
| `enabledGraphImage` | *boolean*       | 関連するグラフをSlackに投稿するか否か |
| `events`            | *array[string]*        | 通知するイベント(`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, `"monitor"` のいずれか) |

#### 応答(Slack)

##### 成功時

```json
{
  "id": <channelId>,
  "type": "slack",
  "name": "My Channel",
  "url": "https://hooks.slack.com/services/TAAAA/BBBB/XXXXX",
  "mentions": {
    "ok": "ok message",
    "warning": "warning message"
  },
  "enabledGraphImage": true,
  "events": ["alert"]
}
```

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
      <td><code>url</code>が不正な場合</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>events</code>に上記以外のイベントが含まれる場合</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h3 id="create-webhook-channel">Webhook</h3>

#### 入力(Webhook)

以下のキーをもつオブジェクト

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `type`   | *string* | 固定文字列`"webhook"` |
| `name`   | *string* | チャンネルの名前 |
| `url`               | *string*        |  HTTPリクエストを受け取るURL      |
| `enabledGraphImage` | *boolean*       | 関連するグラフのURLを通知内容に含めるか否か |
| `events`            | *array[string]*        | 通知するイベント(`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, `"monitor"` のいずれか) |

#### 応答(Webhook)

##### 成功時

```json
{
  "id": <channelId>,
  "type": "webhook",
  "name": "My Channel",
  "url": "https://example.com/webhook",
  "enabledGraphImage": true,
  "events": ["alert"]
}
```

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
      <td><code>url</code>が不正な場合</td>
    </tr>
    <tr>
      <td>400</td>
      <td><code>events</code>に上記以外のイベントが含まれる場合</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">通知チャンネルの更新</h2>

チャンネルを更新します

※現在はメール通知とSlack, Webhook の3つに対応しています

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/channels/<em>&lt;channelId&gt;</em></code>
</p>

### APIキーに必要な情報

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

更新内容は [チャンネルの作成](#create) と同じ形式で指定します。

ただし、`type` は変更できません。

### 応答

#### 成功時

更新後のチャンネルを [チャンネルの作成](#create) と同じ形式で返します

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
        <td>JSONのフォーマットが不正であるとき</td>
    </tr>
    <tr>
        <td>400</td>
        <td>チャンネルの種類を変更しようとしたとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td><code><em>&lt;channelId&gt;</em></code>に対応するチャンネルが見つからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">通知チャンネルの削除</h2>

チャンネルを削除します

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/channels/<em>&lt;channelId&gt;</em></code>
</p>

### APIキーに必要な情報

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

削除される直前のチャンネルの状態が返されます

#### 成功時

フォーマットはチャンネル一覧APIの`<channel>`と同じ形式です。

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
      <td><code><em>&lt;channelId&gt;</em></code>に対応するチャンネルが見つからないとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

----------------------------------------------
