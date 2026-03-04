---
Title: オーガニゼーション
Date: 2016-03-22T11:00:53+09:00
URL: https://mackerel.io/ja/api-docs/entry/organizations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368067612
---

<ul class="internal-nav">
  <li><a href="#get">オーガニゼーションの情報を取得</a></li>
  <li><a href="#ip-restriction-webconsole-post">Webコンソールのアクセス制限設定の登録</a></li>
  <li><a href="#ip-restriction-webconsole-get">Webコンソールのアクセス制限設定の取得</a></li>
  <li><a href="#ip-restriction-api-post">APIのアクセス制限設定の登録</a></li>
  <li><a href="#ip-restriction-api-get">APIのアクセス制限設定の取得</a></li>
</ul>

<h2 id="get">オーガニゼーションの情報を取得</h2>

オーガニゼーションの情報を取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/org</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "name": <name>,
  "displayName": <displayName>
}
```

| KEY            | TYPE     | DESCRIPTION |
| -------------- | -------- | ----------- |
| `name`         | *string* | オーガニゼーションの名前。 |
| `displayName`  | *string* / *null* | オーガニゼーションの管理名。 |

----------------------------------------------

<h2 id="ip-restriction-webconsole-post">Webコンソールのアクセス制限設定の登録</h2>

Webコンソールのアクセス制限設定を登録します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/settings/ip-restriction/web</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

以下のキーをもつオブジェクトです。

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `cidrs` | *array[string]* | オーガニゼーションのWebコンソールにアクセスできるIP範囲のCIDR記法によるホワイトリスト[*1](#cidrs)  |

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
      <td>400</td>
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h4 id="cidrs" class="annotation">*1 cidrs</h4>

アクセス制限を解除するには、 <span class="table-code">cidrs</span> に `[]` を指定します。

----------------------------------------------

<h2 id="ip-restriction-webconsole-get">Webコンソールのアクセス制限設定の取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/settings/ip-restriction/web</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "cidrs": [<cidr>,<cidr>...]
}
```

<i>`<cidr>`</i> は、CIDR記法によるIP範囲

----------------------------------------------

<h2 id="ip-restriction-api-post">APIのアクセス制限設定の登録</h2>

APIのアクセス制限設定を登録します。

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/settings/ip-restriction/api</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

以下のキーをもつオブジェクトです。

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `cidrs` | *array[string]* | オーガニゼーションのAPIにアクセスできるIP範囲のCIDR記法によるホワイトリスト[*2](#cidrs)  |

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
      <td>400</td>
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h4 id="cidrs" class="annotation">*2 cidrs</h4>

アクセス制限を解除するには、 <span class="table-code">cidrs</span> に `[]` を指定します。

----------------------------------------------

<h2 id="ip-restriction-api-get">APIのアクセス制限設定の取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/settings/ip-restriction/api</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "cidrs": [<cidr>,<cidr>...]
}
```

<i>`<cidr>`</i> は、CIDR記法によるIP範囲


