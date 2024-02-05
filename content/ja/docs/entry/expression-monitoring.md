---
Title: 式による監視を行う
Date: 2016-07-28T14:23:05+09:00
URL: https://mackerel.io/ja/docs/entry/expression-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687176424135
---

本機能では、式によって計算されたメトリックに対しての監視を行います。
**当機能は、有償オプションとなります。Freeプランでは利用できません。**また、2018年4月時点で[実験的機能](https://mackerel.io/ja/docs/entry/advanced/experimental-features)となっています。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160728/20160728151137.png)

## 式による監視を設定する

画面左側メニューの監視ルールより監視ルールを追加のボタンをクリックします。 「式による監視」のタブをクリックすると、以下の項目が表示されますので、 各項目に値・名称を記述して作成ボタンをクリックします

- 監視対象: 式エディタに監視対象メトリックを計算するための式を入力してください。式が正しく解釈でき、計算対象メトリックが取得できた場合は右側のpreviewにグラフが描画されます。
- アラートの発生条件：Warning, Criticalの閾値を設定してください。
- 通知の再送間隔：アラートの状態が、指定された時間を超えても変化がない場合、再度通知します。
- 監視ルール名：本監視定義の名前を記述してください

監視ルールで利用可能な関数はこちらを参照してください。

[https://mackerel.io/ja/docs/entry/advanced/advanced-graph#functions:embed:cite]

## 式による監視の仕様について

- 監視間隔は5分ごとになります
- 監視項目として設定可能な式は、グラフの系列が1本になるものだけになります
- 式よってメトリック値が取得できない・式が複雑すぎてタイムアウトする、などの場合は、ステータスがUnknownとしてアラートが発生します
- 監視項目の上限数は20です
- Trialプラン及び有料プランでのみご利用いただけます

<h2 id="future-predictions">監視例：将来予測によるファイルシステム容量が不足するまでの日数の監視</h2>

Mackerelでは `linearRegression()`, `timeLeftForecast()`といった線形回帰の関数を利用できます。
式による監視の一例として、これらの関数を用いた将来予測機能による、ファイルシステムの空き容量が枯渇するまでの残り日数を監視してみましょう。

線形回帰した値が閾値になるまでの秒数を取得する`timeLeftForecast()`を使います。

実際に監視するための式の一例は以下のようになります。

```
scale(timeLeftForecast(host(host_id, filesystem.drive.used), 3mo, 2000000000000), 1/86400)
```

上記は、「指定したホストの`filesystem.drive.used`メトリックの3ヶ月間の値を用いて線形回帰した値が、2TBに達するまでの日数」を得るための式となります。

式をひとつずつ分解して解説していきます。

まず、`host(host_id, filesystem.drive.used)` によって、指定したホストの`filesystem.drive.used` メトリックが得られます。

これに`timeLeftForecast()`を適用します。`timeLeftForecast()`の引数はそれぞれ、"metrics", "duration", "threshold"となり、上記の例では**指定したホスト**の**3ヶ月間**の値を用いて線形回帰した値が、**2000000000000byte(2TB)**に達するまでの秒数が得られることになります。

最後に、得られたファイルシステム枯渇までの秒数を、`scale()`関数を用いて日数に換算しています。

このように、式による監視機能を用いることで、取得したメトリックに対して様々な視点から監視を行えます。
