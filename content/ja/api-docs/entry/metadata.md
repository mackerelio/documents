---
Title: メタデータ
Date: 2016-09-29T11:39:53+09:00
URL: https://mackerel.io/ja/api-docs/entry/metadata
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328749687186828710
---

<ul class="internal-nav">
  <li><a href="#hostget">ホストメタデータの取得</a></li>
  <li><a href="#hostput">ホストメタデータの登録・更新</a></li>
  <li><a href="#hostdelete">ホストメタデータの削除</a></li>
  <li><a href="#hostlist">ホストメタデータの一覧</a></li>
  <li><a href="#serviceget">サービスメタデータの取得</a></li>
  <li><a href="#serviceput">サービスメタデータの登録・更新</a></li>
  <li><a href="#servicedelete">サービスメタデータの削除</a></li>
  <li><a href="#servicelist">サービスメタデータの一覧</a></li>
  <li><a href="#roleget">ロールメタデータの取得</a></li>
  <li><a href="#roleput">ロールメタデータの登録・更新</a></li>
  <li><a href="#roledelete">ロールメタデータの削除</a></li>
  <li><a href="#rolelist">ロールメタデータの一覧</a></li>
</ul>

<h2 id="metadata">メタデータ</h2>

このAPIを用いると、ホスト・サービス・ロールに任意のJSONデータを登録できます。管理用IDや、その他運用上の管理データなど、様々な情報を登録してご利用いただけます。

ネームスペースとは、メタデータの種類を特定する識別子です。例えば`project`や`environment`など、一貫した識別子を用いることを推奨します。メタデータの取得・登録・更新・削除のエンドポイントは一貫して以下のURLとなっています。

<code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt;</em></code>

このエンドポイントにJSON (オブジェクト・配列・文字列・数字・真偽値・null) を登録し、取得できます。
```json
{
  "type": 12345,
  "region": "jp",
  "env": "staging",
  "instance_type": "c4.xlarge"
}
```

ネームスペースにはアルファベットと数字、`_`、`-`を使用できます (`[-a-zA-Z0-9_]+`)。ただし`mackerel`から始まるネームスペースは、Mackerelのシステムが利用します。

<h2 id="hostget">ホストメタデータの取得</h2>

登録されたメタデータを取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

#### 応答

登録されているデータのJSONが返されます。  
レスポンスヘッダの`Last-Modified` にはメタデータの最終更新日時が設定されています。

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
      <td>ホストが退役して一週間以上経過している時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ホストが存在しない時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ホストに指定されたメタデータが存在しない時</td>
    </tr>
  </tbody>
</table>

<h2 id="hostput">ホストメタデータの登録・更新</h2>

任意のJSONをホストのメタデータとして登録と更新を行います。

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

任意のJSONを指定できます。ただしデータのサイズは100KBまでに制限されています。

### 応答

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
      <td>ホストがすでに退役している時</td>
    </tr>
    <tr>
      <td>400</td>
      <td>ネームスペースが不正な値の時</td>
    </tr>
    <tr>
      <td>400</td>
      <td>ホストあたりのメタデータの制限 (1ホストあたり50個) を超過して登録しようとした時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ホストが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>413</td>
      <td>メタデータが100KBを超過している時</td>
    </tr>
  </tbody>
</table>

<h2 id="hostdelete">ホストメタデータの削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

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
      <td>ホストがすでに退役している時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ホストが存在しない時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ホストに指定されたメタデータが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="hostlist">ホストメタデータの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "metadata": [<metadata>, <metadata>, ...]
}
```

`<metadata>` は以下のキーを持つオブジェクト

<table>
  <thead>
    <tr>
      <th>KEY</th> 
      <th>TYPE</th>
      <th>DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>namespace</code></td>
      <td><em>string</em></td>
      <td>ネームスペース</td>
    </tr>
  </tbody>
</table>

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
      <td>ホストが退役して一週間以上経過している時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ホストが存在しない時</td>
    </tr>
  </tbody>
</table>

<h2 id="serviceget">サービスメタデータの取得</h2>

登録されたメタデータを取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

#### 応答

登録されているデータのJSONが返されます。  
レスポンスヘッダの`Last-Modified` にはメタデータの最終更新日時が設定されています。

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
      <td>404</td>
      <td>サービスが存在しない時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>サービスに指定されたメタデータが存在しない時</td>
    </tr>
  </tbody>
</table>

<h2 id="serviceput">サービスメタデータの登録・更新</h2>

任意のJSONをサービスのメタデータとして登録と更新を行います。

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

任意のJSONを指定できます。ただしデータのサイズは100KBまでに制限されています。

### 応答

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
      <td>ネームスペースが不正な値の時</td>
    </tr>
    <tr>
      <td>400</td>
      <td>サービスあたりのメタデータの制限 (1サービスあたり50個) を超過して登録しようとした時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>サービスが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>413</td>
      <td>メタデータが100KBを超過している時</td>
    </tr>
  </tbody>
</table>

<h2 id="servicedelete">サービスメタデータの削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

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
      <td>サービスが存在しない時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>サービスに指定されたメタデータが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="servicelist">サービスメタデータの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "metadata": [<metadata>, <metadata>, ...]
}
```

`<metadata>` は以下のキーを持つオブジェクト

<table>
  <thead>
    <tr>
      <th>KEY</th> 
      <th>TYPE</th>
      <th>DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>namespace</code></td>
      <td><em>string</em></td>
      <td>ネームスペース</td>
    </tr>
  </tbody>
</table>

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
      <td>サービスが存在しない時</td>
    </tr>
  </tbody>
</table>

<h2 id="roleget">ロールメタデータの取得</h2>

登録されたメタデータを取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

#### 応答

登録されているデータのJSONが返されます。  
レスポンスヘッダの`Last-Modified` にはメタデータの最終更新日時が設定されています。

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
      <td>404</td>
      <td>サービス・ロールが存在しない時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ロールに指定されたメタデータが存在しない時</td>
    </tr>
  </tbody>
</table>

<h2 id="roleput">ロールメタデータの登録・更新</h2>

任意のJSONをロールのメタデータとして登録と更新を行います。

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

任意のJSONを指定できます。ただしデータのサイズは100KBまでに制限されています。

### 応答

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
      <td>ネームスペースが不正な値の時</td>
    </tr>
    <tr>
      <td>400</td>
      <td>ロールあたりのメタデータの制限 (1ロールあたり10個) を超過して登録しようとした時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>サービス・ロールが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
    <tr>
      <td>413</td>
      <td>メタデータが100KBを超過している時</td>
    </tr>
  </tbody>
</table>

<h2 id="roledelete">ロールメタデータの削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

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
      <td>サービス・ロールが存在しない時</td>
    </tr>
    <tr>
      <td>404</td>
      <td>ロールに指定されたメタデータが存在しない時</td>
    </tr>
    <tr>
      <td>403</td>
      <td>APIキーに書き込み権限がないとき / <a href="https://support.mackerel.io/hc/ja/articles/360039701952-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%82%92IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E5%88%B6%E9%99%90%E3%81%97%E3%81%9F%E3%81%84" target="_blank">許可されたIPアドレス範囲</a>外からのアクセスの場合</td>
    </tr>
  </tbody>
</table>

<h2 id="rolelist">ロールメタデータの一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  "metadata": [<metadata>, <metadata>, ...]
}
```

`<metadata>` は以下のキーを持つオブジェクト

<table>
  <thead>
    <tr>
      <th>KEY</th> 
      <th>TYPE</th>
      <th>DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>namespace</code></td>
      <td><em>string</em></td>
      <td>ネームスペース</td>
    </tr>
  </tbody>
</table>

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
      <td>サービス・ロールが存在しない時</td>
    </tr>
  </tbody>
</table>
