---
Title: 監視・通知を設定する
Date: 2014-09-02T16:07:16+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815731865539
---

Mackerelでは、登録しているホストを監視し、状況に応じてアラートを通知できます。

## 監視機能を有効にする

* アラートが発生しても通知されない場合、後述の[アラート設定][alerts-settings]、[個人設定ページ][settings-user]の設定が正しいか確認してください。

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

ホストを退役させた際には、そのホストに関連するアラートは自動的に閉じられます。

ホストのステータス変更時には、アラートに対する自動的な操作は一切おこなわれません。

"standby" 状態時に発生したアラートは、その後ホストの状態を "working" に変更したとしても、アラートの発生通知はおこなわれません。このケースでは、その後アラートの状態が変更された場合か、通知の再送間隔が設定されており、その間隔が過ぎた場合に通知がおこなわれます。

ホストの状態を "maintenance" や "poweroff" に変更した場合、その時既に発生していたアラートについては、その後閾値異常や疎通が回復したとしても自動的には閉じられません。"working" や "standby" にステータスを戻し、異常状態が回復すればアラートは自動的に閉じられます。

## 死活監視

[mackerel-agentをインストール・起動][install-agent] したホストに対しては、死活監視が自動的に行なわれます。

死活監視はmackerel-agentからのメトリックの定期投稿を監視しています。一定期間この投稿がない場合、Mackerelはそのホストに異常が発生したと判断してアラートを発生させます。

[install-agent]: https://mackerel.io/ja/docs/entry/howto/install-agent

## メトリック監視

mackerel-agentから投稿されるメトリック（ユーザー定義メトリックを含む）に対して、閾値による監視を行えます。閾値にはWarningとCriticalの2段階を設定可能です。

閾値は投稿される値との比較になりますが、CPU使用率およびメモリ使用率は割合で指定できます。

### 監視ルール

監視ルールには以下の項目が含まれます。

- 名前
- 監視対象のメトリック
- Warning条件
  - 比較演算子
  - 閾値
- Critical条件
  - 比較演算子
  - 閾値
- 平均値監視
  - 複数のメトリック値の平均値に対して監視を行います
- 監視対象のホストが所属するサービスおよびロール
- 最大試行回数
  - 閾値を超えた状態が連続した時にアラートを発生させます。一度でも閾値を下回ればアラートは閉じられます。
- メモ
  - 監視ルールの意図や、アラートが発生した時の対応などを自由に記述できます。
- 通知の再送回数
  - アラートが発生している状態が続いている間、設定された時間間隔で通知を送り続けることができます。

- 注意事項
  - 監視条件の閾値はいつでも変更できます。監視対象メトリックの変更はできません。
  - 閾値を変更すると、既に発生済みのアラートはそのままの状態になりますので、手動で閉じていただく必要があります。
  - 監視対象のホストが所属するサービスおよびロールは複数指定できます。ひとつも指定しない場合、すべてのホストが監視対象となります。
  - 監視対象として指定されているロールがすべて削除された場合、その監視ルールはすべてのホストに対して実行されます。
  - 同じメトリックに対して複数の監視ルールがある場合、それぞれの監視は個別に実行されます。
  - 最新のメトリックのタイムスタンプが現在時刻よりも20分以上古い場合と、以前に送られたメトリックよりも古いタイムスタンプのメトリックが送られた場合は、監視が実行されません

### 監視ルールを作成する

[監視ルール一覧画面][monitors]で監視ルールを一覧できます。この画面右上の「監視ルールを追加」から監視条件を新規作成できます。

[monitors]: https://mackerel.io/my/monitors

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

[通知チャンネルの設定][channels-settings]より、アラートの通知チャンネルを編集できます。対応している通知チャンネルはEメールとWebhookの他に、Slack、HipChat、TypeTalk、Chatwork、PagerDuty など多くのWebサービスと連携可能になっています。対応チャンネルは随時追加していきます。

[channels-settings]: https://mackerel.io/my/channels?new
[alerts-settings]: https://mackerel.io/my/monitors

メールブロードキャストはそのオーガニゼーションに所属しているすべてのメンバーにメールを一斉送信します（[個人設定により受け取らないように変更できます](#optout-broadcast-notification-email)）。その他の通知チャンネルの設定の詳細については、ヘルプのサイドバー「アラート/alerts」をご確認下さい。

<h3 id="optout-broadcast-notification-email">通知メールを受け取らない（個人設定）</h3>

[個人設定ページ][settings-user]より、アラート一斉送信通知メールを受信しない設定にできます。

[settings-user]: https://mackerel.io/settings/user

[alerts-settings]: https://mackerel.io/my/monitors


<h3 id="mute-notification">監視ルール単位で通知を抑制する</h3>

[監視ルール一覧画面][monitors]で操作することにより、監視ルール単位で通知の抑制（ミュート）を設定できます。

[https://cdn-ak2.f.st-hatena.com/images/fotolife/m/mackerelio/20171115/20171115171542.png:image]

[Web API](https://mackerel.io/ja/api-docs/entry/monitors) からも設定可能です。一時的に通知を抑制したい場合などに活用してください。


<h2 id="notification-group">通知グループで柔軟な通知条件を設定する</h2>

Mackerelでは、通知グループを作成することでチームの通知チャンネルに特定のサービスの通知のみを行うなどの柔軟な通知条件を設定できます。通知グループは通知チャンネルと同じように[通知チャンネル設定][channels-settings] のページから作成することが出来ます。

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190204/20190204165515.png:image]


設定したサービス/監視ルールに関する通知のみが、通知グループに所属するチャンネルに送られるようになります。

### 全ての通知を受け取る通知グループ

オーガニゼーションにはそれぞれ一つ全ての通知を受け取るデフォルト通知グループが存在します。新しい通知チャンネルを登録した時は自動的にデフォルト通知グループに所属します。

### 対象となる通知グループを限定する

通知グループに監視ルールを設定するときに 「この通知グループのみに通知する」オプションを有効にすると、設定した監視ルールからの通知はその通知グループにのみ通知され、他の通知グループには通知されなくなります。

### 設定例

`CPU%` と `connectivity` の監視ルールによるアラート通知は 運用チームの Chatwork に通知し、それ以外は Email と Slack に通知する場合は下図のようになります。
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190205/20190205151701_original.png:image]

### 通知レベル

`Critical only`がOnの場合、Warningは通知されません。詳細な通知条件は以下のとおりです。

- Critical以上(Critical, Unknown)のエラーレベルは通知される
- Critical以上になったことのあるアラートのOKは通知される
