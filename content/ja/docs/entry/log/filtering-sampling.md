---
Title: フィルタリングとサンプリングによるログデータ量の最適化
Date: 2026-07-16T11:15:57+09:00
URL: https://mackerel.io/ja/docs/entry/log/filtering-sampling
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032054079159
---

ログの送信データ量を抑えるための、**OpenTelemetryコレクター（以下、コレクター）**でのフィルタリングとサンプリングの設定方法を解説します。

<div class="note">
    <p>Mackerelのログ機能は現在β版として提供しています。以下の点についてはあらかじめご了承ください。</p>
</div>

- 今後、仕様や画面デザインが変更される可能性があります。
- 機能改善や不具合修正のために、臨時のメンテナンスを実施する場合があります。
- お預かりしているログデータの保持を保証することはできません。

**目次**

[:contents]

## はじめに

ログの送信を始めてみたところ、データ量やコストが想定より多かったということはないでしょうか。「[ログのデータ量とコストを見積もる](https://mackerel.io/ja/docs/entry/log/cost-estimation)」ヘルプの手順でインジェスト量を確認し、削減が必要と判断した場合は、このヘルプで紹介する手法を検討してみてください。

全ログをそのまま送信し続けるのではなく、**「必要なログだけを残す」**という考え方が重要です。

インジェスト量の削減には、アプリケーション側でログの出力量自体を調整する方法（ログレベルの引き上げや、不要なログ出力の見直しなど）もありますが、このヘルプではアプリケーションのコード変更なしに実現できるコレクター側のアプローチとして、以下の手法を紹介します。

* **フィルタリング**：条件に合致する不要なログを送信前に除外する
* **サンプリング**：ログの一定割合だけを送信し、残りを間引く

いずれもコレクターの設定ファイル（`config.yaml`）を編集することで実現できます。アプリケーション側のコード変更は不要です。

## フィルタリング：不要なログを除外する

### フィルタリングの考え方

**フィルタリング**は、コレクターがログを受信した時点で不要なログを判別し、Mackerelへの送信対象から除外する仕組みです。除外されたログはインジェスト量にカウントされないため、直接的にデータ量の削減につながります。

どのログを「不要」と判断するかはサービスの特性によりますが、以下は多くの環境で除外を検討できる一般的な例です。

* **DEBUG / TRACE レベルのログ**：本番環境では通常不要なケースが多いログレベル
* **ヘルスチェックや定期ポーリングのログ**：`/healthz`、`/readyz` などへのリクエストで大量に出力される定型ログ
* **特定のパスやメッセージパターンのログ**：調査に不要な定型的なログメッセージ

フィルタリングは「条件に合致するログを全件除外する」動作のため、本当に不要なログかどうかを慎重に判断してください。迷う場合は、まずはDEBUG / TRACEレベルの除外から始めるのが安全です。

### コレクターでのフィルタリング設定

コレクターの **filter プロセッサー** を使うと、ログレベル（Severity Number）やメッセージの内容に基づいてログを除外できます。

以下に、OpenTelemetry Collector Contrib での設定例を示します。

##### 例1：DEBUG / TRACE レベルのログを除外する

Severity Number が 8 以下（DEBUG / TRACE に相当）のログを除外し、INFO 以上のログだけを送信する設定です。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: "0.0.0.0:4317"
      http:
        endpoint: "0.0.0.0:4318"

processors:
  filter/drop-debug:
    logs:
      log_record:
        - severity_number < 9

exporters:
  otlp_http/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": "${env:MACKEREL_APIKEY}"
    sending_queue:
      batch:
        max_size: 3500000
        sizer: bytes

service:
  pipelines:
    logs:
      receivers: [otlp]
      processors: [filter/drop-debug]
      exporters: [otlp_http/mackerel]
```

filter プロセッサーの `logs.log_record` に条件を記述します。条件に**合致した**ログが**除外**される点に注意してください。上記の例では `severity_number < 9` に合致するログ（TRACE / DEBUG）が除外されます。

MDOT コレクターを利用する場合も、エクスポーターを `mackerel_otlp` に置き換えるだ
けで、それ以外は同じです。

##### 例2：特定のメッセージパターンを含むログを除外する

ヘルスチェックエンドポイントへのリクエストログなど、特定の文字列パターンを含むログを除外する設定です。

```yaml
processors:
  filter/drop-healthcheck:
    logs:
      log_record:
        - IsMatch(body, ".*(/healthz|/readyz).*")
```

この設定では、ログ本文に `/healthz` または `/readyz` を含むログが除外されます。

複数のフィルタ条件を組み合わせることも可能です。

```yaml
processors:
  filter/drop-debug:
    logs:
      log_record:
        - severity_number < 9
  filter/drop-healthcheck:
    logs:
      log_record:
        - IsMatch(body, ".*(/healthz|/readyz).*")

service:
  pipelines:
    logs:
      receivers: [otlp]
      processors: [filter/drop-debug, filter/drop-healthcheck]
      exporters: [otlp_http/mackerel]
```

`processors` の実行順序は `service.pipelines` の記述順に従います。上記の例では、まず DEBUG / TRACE レベルのログが除外され、次にヘルスチェック関連のログが除外されます。

### 不要なリソース属性の削減

ログのデータ量に影響するのはメッセージ本文だけではありません。OpenTelemetry SDK やライブラリ計装が自動付与する**リソース属性**は、ログ1件ごとに付随するため、データ量への影響が大きくなる場合があります。

実運用では、`telemetry.sdk.version`、`telemetry.sdk.language`、`os.type` といったリソース属性が自動的に多数付与され、想定以上にインジェスト量が膨らむケースがあります。Mackerelのログ検索で使わない属性を削減することで、効果的にデータ量を抑えられます。

コレクターの **resource プロセッサー** や **transform プロセッサー** を使って、不要なリソース属性を削除できます。特定のキーを削除するだけであれば resource プロセッサーがシンプルです。条件に応じた操作や、リソース属性以外のログ属性も合わせて操作したい場合は transform プロセッサーを使います。

##### 例3：resource プロセッサーで不要な属性を削除する

```yaml
processors:
  resource/remove-unnecessary:
    attributes:
      - key: telemetry.sdk.version
        action: delete
      - key: telemetry.sdk.language
        action: delete
      - key: os.type
        action: delete
      - key: os.description
        action: delete

service:
  pipelines:
    logs:
      receivers: [otlp]
      processors: [resource/remove-unnecessary]
      exporters: [otlp_http/mackerel]
```

##### 例4：transform プロセッサーで属性を削除する

transform プロセッサーを使えば、条件を組み合わせたより柔軟な属性の操作が可能です。

```yaml
processors:
  transform/remove-resource-attrs:
    log_statements:
      - context: resource
        statements:
          - delete_key(attributes, "telemetry.sdk.version")
          - delete_key(attributes, "telemetry.sdk.language")
          - delete_key(attributes, "os.type")
          - delete_key(attributes, "os.description")

service:
  pipelines:
    logs:
      receivers: [otlp]
      processors: [transform/remove-resource-attrs]
      exporters: [otlp_http/mackerel]
```

##### 削除してよい属性の判断基準

どの属性を削除するかは、以下の基準で判断できます。

* **Mackerelのログ検索で使わない属性**：ログの検索や絞り込みに使用しない属性は削除候補
* **SDK が自動付与するデフォルト属性**：`telemetry.sdk.version`、`telemetry.sdk.language` など、障害調査には通常不要な属性
* **環境情報系の属性**：`os.type`、`os.description` など、サービス運用上の調査に使わない属性

逆に、`service.name` はMackerelのサービス選択に使用する重要な属性のため、削除しないでください。属性を削除する際は、自環境で付与されているリソース属性を確認し、実際に調査で使っていないものから段階的に削除することをお勧めします。

## 機微情報の加工

ログには、メールアドレスや電話番号、認証トークンなどの機微情報が含まれていることがあります。このような情報をそのまま送信するのではなく、コレクターの transform プロセッサーを使って送信前に加工できます。

以下の例では、Filelog Receiver の JSON パーサーなどによってログボディが構造化された状態（マップ形式）であることを前提としています。たとえば、次のような JSON ログを想定します。

```json
{"message": "User login succeeded", "email": "user@example.com", "phone": "090-1234-5678", "token": "eyJhbGciOiJSUzI1NiIs..."}
```

##### 例5：機微情報をマスキングする

フィールドの値を固定の文字列で置き換えます。元の値は失われますが、フィールドの存在自体は残るため、「このログにはメールアドレスが含まれていた」という事実は確認できます。

```yaml
processors:
  transform/mask-sensitive:
    log_statements:
      - context: log
        statements:
          - set(body["phone"], "***") where IsMap(body)
          - set(body["email"], "***") where IsMap(body)
```

##### 例6：機微情報をハッシュ化する

フィールドの値を SHA-256 ハッシュに変換します。元の値は復元できませんが、同じ値は常に同じハッシュになるため、「同一ユーザーに関するログ」をハッシュ値で突合できます。

```yaml
processors:
  transform/hash-sensitive:
    log_statements:
      - context: log
        statements:
          - set(body["email"], SHA256(body["email"])) where IsMap(body)
```

##### 例7：機微情報を除去する

フィールド自体をログから削除します。マスキングやハッシュ化と異なり、フィールドの存在も残りません。認証トークンなど、存在自体を記録する必要がない情報に適しています。

```yaml
processors:
  transform/remove-sensitive:
    log_statements:
      - context: log
        statements:
          - delete_key(body, "token") where IsMap(body)
```

これらは組み合わせて使うこともできます。たとえば、メールアドレスはハッシュ化して突合に使い、電話番号はマスキング、トークンは除去する、といった使い分けが可能です。

```yaml
processors:
  transform/sanitize:
    log_statements:
      - context: log
        statements:
          - set(body["email"], SHA256(body["email"])) where IsMap(body)
          - set(body["phone"], "***") where IsMap(body)
          - delete_key(body, "token") where IsMap(body)
```

## サンプリング：ログの量を間引く

### サンプリングの考え方

フィルタリングが「条件に合致するログを全件除外する」のに対して、**サンプリング**は「ログの一定割合だけを残し、残りを間引く」アプローチです。

たとえば、正常に動作しているときの INFO レベルのログはすべてを保持する必要がないかもしれません。一方で、ERROR や FATAL のログは障害調査に不可欠なため、1件も漏らさず残したいケースが多いでしょう。

サンプリングを使えば、このような「正常系のログは間引き、異常系のログは全件残す」という使い分けが可能です。

ただし、サンプリングには**間引かれたログは復元できない**というリスクがあります。障害調査の際に、間引かれた INFO ログの中に手がかりがあったというケースも起こり得ます。まずはフィルタリングや不要な属性の削減で十分なコスト削減が得られないか検討し、それでもインジェスト量を抑えたい場合にサンプリングの導入を検討することをお勧めします。

### 確率的サンプリング

**確率的サンプリング（Probabilistic Sampling）** は、ログの内容に関わらず一定の割合だけを送信する方式です。

以下は、ログ全体の10%程度を送信する設定例です。

<div class="note">
    <p>probabilistic_sampler processorは送信されるログに Trace IDが付与されている(0ではない)場合のみ利用可能です。</p>
    <p>Trace IDが0の場合は<a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/processor/probabilisticsamplerprocessor/README.md">probabilistic_sampler processorのREADME</a>を参考に別カラムを利用するなどの対応が必要です。</p>
</div>

<br>
```yaml
processors:
  probabilistic_sampler:
    sampling_percentage: 10

service:
  pipelines:
    logs:
      receivers: [otlp]
      processors: [probabilistic_sampler]
      exporters: [otlp_http/mackerel]
```

確率的サンプリングはシンプルですが、ERROR ログも同じ割合で間引かれてしまう点に注意が必要です。重要なログを漏らさず残したい場合は、次に紹介する条件付きサンプリングを検討してください。

### 条件付きサンプリング

重要度に応じてサンプリング率を変えることで、重要なログを確実に残しつつ、全体のデータ量を抑えられます。以下は、ログレベルごとにサンプリング率を変える設定例です。

| 重要度 | サンプリング率 | 説明 |
|---|---|---|
| ERROR / FATAL | 100%（全件送信） | 障害調査に必要なため全件残す |
| WARN | 50% | 半数を送信 |
| INFO | 10% | 正常系のため大幅に間引く |
| DEBUG / TRACE | 0%（送信しない） | 本番では不要なため除外 |

この動作を実現するには、filter プロセッサーと probabilistic_sampler プロセッサーを組み合わせ、複数のパイプラインを定義します。

```yaml
processors:
  # DEBUG / TRACE を除外
  filter/drop-debug:
    logs:
      log_record:
        - severity_number < 9

  # ERROR / FATAL のみ通す（全件送信用）
  filter/keep-error:
    logs:
      log_record:
        - severity_number < 17

  # WARN のみ通す
  filter/keep-warn:
    logs:
      log_record:
        - severity_number < 13 or severity_number > 16

  # INFO のみ通す
  filter/keep-info:
    logs:
      log_record:
        - severity_number < 9 or severity_number > 12

  # WARN 用：50% サンプリング
  probabilistic_sampler/warn:
    sampling_percentage: 50

  # INFO 用：10% サンプリング
  probabilistic_sampler/info:
    sampling_percentage: 10


exporters:
  otlp_http/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": "${env:MACKEREL_APIKEY}"
    sending_queue:
      batch:
        max_size: 3500000
        sizer: bytes

connectors:
  forward:

service:
  pipelines:
    # 受信したログを各コネクターに分岐
    logs/intake:
      receivers: [otlp]
      processors: [filter/drop-debug]
      exporters: [forward]

    # ERROR / FATAL：全件送信
    logs/error:
      receivers: [forward]
      processors: [filter/keep-error]
      exporters: [otlp_http/mackerel]

    # WARN：50% サンプリング
    logs/warn:
      receivers: [forward]
      processors: [filter/keep-warn, probabilistic_sampler/warn]
      exporters: [otlp_http/mackerel]

    # INFO：10% サンプリング
    logs/info:
      receivers: [forward]
      processors: [filter/keep-info, probabilistic_sampler/info]
      exporters: [otlp_http/mackerel]
```

この設定では、forward コネクターを使ってログを重要度ごとのパイプラインに分岐させ、それぞれ異なるサンプリング率を適用しています。環境に応じてサンプリング率を調整してください。

## 最適化の進め方

フィルタリングとサンプリングのどちらから始めるべきか迷う場合は、以下の順序で段階的に進めることをお勧めします。削減効果が大きく、かつリスクの低い対策から適用していきましょう。

1. **不要なログレベルの除外（DEBUG / TRACE）**
   多くの環境で効果が大きく、本番運用で不要なケースがほとんどのため、最初に検討してください
2. **不要なリソース属性の削減**
   SDK が自動付与する属性の中から、調査に使わないものを削除します
3. **定型ログのフィルタリング（ヘルスチェック等）**
   大量に出力されるパターンが明確なログを除外します
4. **正常系ログのサンプリング**
   上記の対策でも削減が不十分な場合に、INFO レベルなどの正常系ログにサンプリングを適用します

設定変更後は、「[ログのデータ量とコストを見積もる](https://mackerel.io/ja/docs/entry/log/cost-estimation)」ヘルプの手順でインジェスト量を再確認し、削減効果を検証してください。想定通りの効果が得られない場合は、フィルタリング条件の見直しやサンプリング率の調整を行いましょう。

## まとめ

このヘルプでは、コレクターの設定によるログデータ量の最適化手法を紹介しました。

* **フィルタリング**：条件に合致する不要なログを送信前に除外する。DEBUG / TRACE レベルの除外やヘルスチェックログの除外など、「確実に不要」と判断できるログに有効
* **リソース属性の削減**：SDK が自動付与する不要な属性を削除し、ログ1件あたりのデータサイズを抑える
* **サンプリング**：ログの一定割合を間引く。正常系は間引きつつ異常系は全件残すといった柔軟な設定が可能

いきなりすべてを適用するのではなく、まずはデータ量への影響が大きい対策から段階的に進め、設定変更ごとにインジェスト量を確認することをお勧めします。

### 関連ヘルプ

* 「[Mackerel のログ機能](https://mackerel.io/ja/docs/entry/log/introduction)」 - ログ機能の概要と活用シーン
* 「[Mackerel にログを送信する](https://mackerel.io/ja/docs/entry/log/sending)」 - コレクターの基本設定とログ送信の手順
* 「[ログのデータ量とコストを見積もる](https://mackerel.io/ja/docs/entry/log/cost-estimation)」 - インジェスト量の確認方法と見積もりの手順
* 「[ログの検索・分析](https://mackerel.io/ja/docs/entry/log/search-and-analysis)」 - Webコンソールでのログ検索機能の使い方
