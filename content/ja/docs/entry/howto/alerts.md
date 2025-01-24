---
Title: 監視・通知を設定する
Date: 2014-09-02T16:07:16+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815731865539
---

Mackerelでは、登録しているホストを監視し、状況に応じてアラートを通知できます。

[:contents]

## 監視機能を有効にする

* アラートが発生しても通知されない場合、後述の[アラート設定][alerts-settings]や[アカウント設定][settings-user]が正しいか確認してください。

[org-setting]: https://mackerel.io/my?tab=setting

<h2 id="host-statuses">監視対象となるホスト</h2>

* 監視対象となるホストは、そのステータスが "working" または "standby" であるもののみです。
* 新規登録されたホストのステータスのデフォルト値の設定は[オーガニゼーショントップ]→[設定]タブ より行えます。もしくは https://mackerel.io/my?tab=setting リンクより直接設定も可能です。 初期状態を”working”にすることにより、ホスト登録直後からアラート通知を受け取れます。
* ステータスが "standby" であるホストについては後述する通知が行われず、ウェブでの確認のみとなります。

| ステータス | 監視 | 通知 |
| ---------- | ---- | ---- |
| <span style="background: #4dbddb;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">working</span> | される | される |
| <span style="background: #70bf16;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">standby</span> | される | されない |
| <span style="background: #f4bd0b;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">maintenance</span> | されない | されない |
| <span style="background: #c6cacc;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">poweroff</span> | されない | されない |

### ホストの退役やステータス変更時のアラートの扱いについて

ホストを退役させた際には、そのホストに関連するアラートは自動的に閉じられます。この自動的に閉じられたアラートの復旧通知は送られません。

ホストステータスの変更時には、アラートに対する自動的な操作は一切おこなわれません。

"standby" 状態時に発生したアラートは、その後ホストステータスを "working" に変更したとしても、アラートの発生通知はおこなわれません。このケースでは、その後アラートの状態が変更された場合か、通知の再送間隔が設定されており、その間隔が過ぎた場合に通知がおこなわれます。

ホストステータスを "maintenance" や "poweroff" に変更した場合、その時既に発生していたアラートについては、その後閾値異常や疎通が回復したとしても自動的には閉じられません。"working" や "standby" にステータスを戻し、異常状態が回復すればアラートは自動的に閉じられます。

## 監視ルールを作成する

[監視ルール一覧画面][monitors] で監視ルールを一覧できます。画面右上の「監視ルールを追加」から監視ルールを新規作成できます。

[monitors]: https://mackerel.io/my/monitors

### 死活監視

[mackerel-agent][install-agent]をインストール・起動したホストに対しては、死活監視が自動的に行なわれます。

死活監視はmackerel-agentからのメトリックの定期投稿を監視しています。一定期間この投稿がない場合、Mackerelはそのホストに異常が発生したと判断してアラートを発生させます。

[install-agent]: https://mackerel.io/ja/docs/entry/howto/install-agent

### ホストメトリック監視・サービスメトリック監視

ホストやサービスに投稿されたメトリックに対して、閾値による監視を行えます。閾値にはWarningとCriticalの2段階を設定可能です。

監視ルールには以下の項目が含まれます。

- メトリック
  - 監視対象のメトリックを指定します。
    - メトリックの種類については [メトリック仕様](https://mackerel.io/ja/docs/entry/spec/metrics) を参照してください。
  - 一部のメトリックは使用率の監視が可能です。
    - [割合監視「CPU %」などの監視ルールについて](https://support.mackerel.io/hc/ja/articles/360040107951-%E5%89%B2%E5%90%88%E7%9B%A3%E8%A6%96-CPU-%E3%81%AA%E3%81%A9%E3%81%AE%E7%9B%A3%E8%A6%96%E3%83%AB%E3%83%BC%E3%83%AB%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)
- 閾値
  - Warning条件
    - 比較演算子
    - 閾値
  - Critical条件
    - 比較演算子
    - 閾値
  - 平均値監視
    - 複数のメトリック値の平均値に対して監視を行います。
- アラート発生までの最大試行回数
  - 閾値を超えた状態が連続した時にアラートを発生させます。一度でも閾値を下回ればアラートは閉じられます。
- 絞込（ホストメトリック監視のみ）
  - 監視対象のホストが所属するサービスおよびロール。
- 途切れ監視（サービスメトリック監視のみ）
  - メトリックの投稿が途絶えてから設定された時間経過するとアラートを発生させます。
- 基本設定
  - 監視ルール名
    - 管理ルールの名前。重複可。
  - 監視ルールのメモ
    - 監視ルールの意図や、アラートが発生した時の対応などを自由に記述できます。この内容はアラート詳細画面やアラート通知に表示されます。
- オプション
  - 通知の再送間隔
    - アラートが発生している状態が続いている間、設定された時間間隔で通知を送り続けることができます。

- 注意事項
  - 監視ルールは監視対象のメトリックが投稿されたホストに適用されます。監視対象のメトリックが一度も投稿されたことがないホストには適用されません。
    - 平均値監視の指定がある場合は、指定した点数分のメトリックが投稿された時点で適用されます。
  - 監視条件の閾値はいつでも変更できます。監視対象メトリックの変更はできません。
  - 閾値を変更すると、既に発生済みのアラートはそのままの状態になりますので、手動で閉じていただく必要があります。
  - 監視対象のホストが所属するサービスおよびロールは複数指定できます。ひとつも指定しない場合、すべてのホストが監視対象となります。
  - 監視対象として指定されているロールがすべて削除された場合、その監視ルールはすべてのホストに対して実行されます。
  - 同じメトリックに対して複数の監視ルールがある場合、それぞれの監視は個別に実行されます。
  - 最新のメトリックのタイムスタンプが現在時刻よりも20分以上古い場合と、以前に送られたメトリックよりも古いタイムスタンプのメトリックが送られた場合は、監視が実行されません。

### 外形監視

[URL外形監視を行う](https://mackerel.io/ja/docs/entry/external-monitoring) を参照してください。

### 式による監視

[式による監視を行う](https://mackerel.io/ja/docs/entry/expression-monitoring) を参照してください。

### ロール内異常検知

[ロール内異常検知による監視を行う](https://mackerel.io/ja/docs/entry/howto/anomaly-detection-for-roles) を参照してください。

### クエリによる監視

[クエリによる監視を行う](https://mackerel.io/ja/docs/entry/query-monitoring) を参照してください。

## アラートを確認する

[アラート一覧画面][alerts]でアラートを確認できます。

### アラートを閉じる

発生したアラートは異常状態が回復すれば（ホストからの疎通が復活する・メトリックが閾値を外れる）自動的に閉じられますが、[アラート一覧画面][alerts]や個別のアラート画面から「アラートを閉じる」ボタンにより手動で閉じることもできます。

[alerts]: https://mackerel.io/my/alerts

### アラート通知を抑制する

[アラート設定][alerts-settings]より、アラート通知を一時的・恒久的に抑制できます。この設定がDisabledまたはSuspendedになっている場合、アラートが生成される際に通知を行いません（アラートの生成は通常どおり行われます）。

### アラートをまとめる
アラートをまとめるアラートグループ機能については以下のドキュメントを参考してください。
[https://mackerel.io/ja/docs/entry/howto/alert-groups:embed]

## 通知

Mackerelは、アラートが生成された際・アラートの状態に変化があった際・閉じられた際に通知をおこないます。

[通知チャンネルの設定][channels-settings]より、アラートの通知チャンネルを編集できます。対応している通知チャンネルはEメールとWebhookの他に、Slack、TypeTalk、Chatwork、PagerDuty など多くのWebサービスと連携可能になっています。対応チャンネルは随時追加していきます。

[channels-settings]: https://mackerel.io/my/channels/-/create
[alerts-settings]: https://mackerel.io/my/monitors

メールブロードキャストはそのオーガニゼーションに所属しているすべてのメンバーにメールを一斉送信します（[アカウント設定](#optout-broadcast-notification-email)により受け取らないように変更できます）。その他の通知チャンネルの設定の詳細については、ヘルプのサイドバー「アラート/alerts」をご確認下さい。

<h3 id="optout-broadcast-notification-email">通知メールを受け取らない（アカウント設定）</h3>

[アカウント設定ページ][settings-user]の[メール通知](https://mackerel.io/settings/user?tab=emailNotifications)で「アラート通知」をOffにすると、通知メールを受信しない設定にできます。この設定はチャンネル設定の「通知するイベント」すべてが対象になります。

[settings-user]: https://mackerel.io/settings/user
[alerts-settings]: https://mackerel.io/my/monitors


<h3 id="mute-notification">監視ルール単位で通知を抑制する</h3>

[監視ルール一覧画面][monitors]で操作することにより、監視ルール単位で通知の抑制（ミュート）を設定できます。

[https://cdn-ak2.f.st-hatena.com/images/fotolife/m/mackerelio/20171115/20171115171542.png:image]

[Web API](https://mackerel.io/ja/api-docs/entry/monitors) からも設定可能です。一時的に通知を抑制したい場合などに活用してください。


<h2 id="notification-group">通知グループで柔軟な通知条件を設定する</h2>

Mackerelでは、通知グループを作成することでチームの通知チャンネルに特定のサービスの通知のみを行うなどの柔軟な通知条件を設定できます。通知グループは通知チャンネルと同じように[通知チャンネル設定][channels-settings]のページから作成することが出来ます。

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190204/20190204165515.png:image]


設定したサービス/監視ルールに関する通知のみが、通知グループに所属するチャンネルに送られるようになります。

### 全ての通知を受け取る通知グループ

オーガニゼーションにはそれぞれ一つ全ての通知を受け取るデフォルト通知グループが存在します。新しい通知チャンネルを登録した時は自動的にデフォルト通知グループに所属します。

### 対象となる通知グループを限定する

通知グループの通知対象に監視ルールを設定する際、監視ルール名の左にあるアイコンをクリックして「この通知グループのみに通知する」オプションを有効にすると、設定した監視ルールからの通知はその通知グループにのみ通知され、他の通知グループには通知されなくなります。

<figure class="figure-image figure-image-fotolife" title="設定箇所"><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230313/20230313183923.png" width="500" height="auto" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span><figcaption>設定箇所</figcaption></figure>

### 設定例

`CPU%` と `connectivity` の監視ルールによるアラート通知は 運用チームの Chatwork に通知し、それ以外は Email と Slack に通知する場合は下図のようになります。
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190205/20190205151701_original.png:image]

### 通知レベル

`Critical only`がOnの場合、Warningは通知されません。詳細な通知条件は以下のとおりです。

- Critical以上(Critical, Unknown)のエラーレベルは通知される
- Critical以上になったことのあるアラートのOKは通知される
