---
Title: mackerel-plugin-accesslog徹底解説
Date: 2017-12-10T00:26:27+09:00
URL: https://mackerel.io/ja/blog/entry/advent-calendar2017/day9-mackerel-plugin-accesslog
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/8599973812325137342
---

Mackerelプロダクトオーナー(肩書き上はサブプロデューサー)の id:Songmu:detail です。この記事は[Mackerel Advent Calendar 2017](https://qiita.com/advent-calendar/2017/mackerel) の9日目の記事です。

今年、[mackerel-plugin-accesslog](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-accesslog) という公式プラグインを書いたのですが、これが便利であり、それなりに頑張って作ったというのもあるので紹介したいと思います。

これは、Webサーバーのアクセスログを集計して、可視化してくれるものです。

## インストール方法

mackerel-plugin-accesslogは公式のメトリックプラグインパッケージであるmackerel-agent-pluginsに含まれているため、それがインストールされていればすぐに利用可能です。パッケージのインストール方法は以下のページを参照してください。

[https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins:title]

Goの環境がある場合は、以下のように `go get` することも可能です。

```
% go get github.com/mackerelio/mackerel-agent-plugins/mackerel-plugin-accesslog
```

## 設定方法

mackerel-agent.conf に以下のように指定します。引数にアクセスログのファイル名を指定するだけ非常に簡単です。

```
[plugin.metrics.accesslog]
command = "mackerel-plugin-accesslog /path/to/access.log"
```

## 対応フォーマット

以下のログフォーマットに対応しています。

- ApacheやNginxで使われている一般的なApache形式のログ(Common/Combined)
- LTSVログアクセスログ

メジャーなApacheログの他に、はてなで統一的に使われており[^1]、日本のWeb業界ではそれなりに使われるようになったLTSV(Labeled Tab-separated Values)フォーマットのアクセスログ形式をサポートしています。

LTSVアクセスログは、 [ltsv.org](http://ltsv.org/)[^2] で定められているRecommended Labelを利用した形式である必要があります。設定方法についても、ltsv.orgに記述がありますのでご参考ください。

アクセスログパーザーを頑張って書いた[^3]ので、大方のログをパーズ可能だと自負しております。ただ、実は、LTSVログでの利用を強くオススメします。LTSVの場合のみ、レスポンスのレイテンシの集計が可能です。また、微々たる差だとは思いますが、LTSVのほうがパーズ効率も良いと思われます。

## グラフ化される項目

以下の項目が集計されグラフ化されます。

- ステータスコード毎のアクセス数
- ステータスコード毎の割合
- レイテンシ(LTSVログの場合のみ対応)
  - 平均
  - 90/95/99パーセンタイル

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170630/20170630090405.png)

スクリーンショットを見ると、非常にわかりやすく可視化されることがわかるでしょう。

ちなみに、mackerel-plugin-accesslogは前回実行時に読み出したログファイル内の位置を記録しており、実行時はその続きからログを読み出します。そのためagentから定期実行された場合には、1分ごとの値が集計されます。

また、ログローテートされた場合であっても、可能な限りローテートされる前のファイルを見つけ出し、見つけた場合は、その末尾までログを読みきった後に、新しいログファイルを最初から読み込む挙動となっています。それにより、より正確な集計が実現されています。このあたりの制御は、postailerというライブラリ[^4]により実現されています。

## 式グラフと組み合わせてロール単位で集計をおこなう

Webサーバーは複数台構成が一般的であるため、ロールで集計したグラフを見たいこともあるでしょう。

Mackerelには式を用いてカスタマイズしたグラフを表示する実験的機能(通称:式グラフ)があります[^5]。この機能を使えばロール単位でのアクセスログを集計したグラフを表示させることができます。

ここでは、ロール単位でのステータスコード毎のアクセス数と割合の積み上げグラフを表示させてみます。また、式グラフは実験的機能であるため、オーガニゼーションの設定で実験的機能を利用する設定を有効にするようにしてください。[^6]

[https://mackerel.io/ja/docs/entry/advanced/advanced-graph:embed]

### ステータスコード別のアクセス数の積み上げグラフを表示する

![](https://cdn-ak2.f.st-hatena.com/images/fotolife/m/mackerelio/20171209/20171209234139_original.png)

上記のグラフを表示させるための式は以下のようになります。ロール名やメトリック名などは適宜書き換えてください。

```
group(
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.2xx_count)),
    '2xx_count')),
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.3xx_count)),
    '3xx_count')),
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.4xx_count)),
    '4xx_count')),
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.5xx_count)),
    '5xx_count')))
```

### ステータスコード別のアクセス割合の積み上げグラフを表示する

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20171209/20171209233325.png)

上記のグラフを表示させるための式は少し複雑ですが以下のようになります。こちらもロール名やメトリック名などは適宜書き換えてください。

```
group(
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.2xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '2xx_rate')),
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.3xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '3xx_rate')),
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.4xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '4xx_rate')),
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.5xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '5xx_rate')))
```

### 式に対して監視をおこなう

式で求めた値に対して監視をおこなうことも可能です。詳しくはヘルプをご覧ください。

[https://mackerel.io/ja/docs/entry/expression-monitoring:embed]

式グラフは強力な機能であり、正しく有用な式が書けた場合は非常に満足感が高いのですが、まだまだ使いづらい機能だとは認識しているため、機能改善につとめていきます。ご要望もお待ちしています。

## fluentdを用いた集計方法との比較

公式ヘルプの「[fluentdでサービスメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/fluentd)」では、fluentdを組み合わせてアクセスログ集計を実現する方法を紹介[^7]しています。

mackerel-plugin-accesslogを使えば、fluentdを使わずともアクセスログ集計が実現できるため非常にお手軽です。

ただ、fluentdを用いた手法が必要なくなるかというとそういうわけはありません。mackerel-plugin-accesslogはあくまで、一つのサーバーのアクセスログに対して決まった集計をおこなうものです。複数サーバーであったり独自の集計をおこなう局面ではfluentdを利用したほうが柔軟で有用な場合もあるでしょう。また、近年ログをサーバーローカルのファイルシステムには書かず、それこそfluentdなどのログコレクタに任せてしまう運用も増えてきており、その場合には、mackerel-plugin-accesslogを利用する事はできません。

## まとめ

mackerel-plugin-accesslog、便利ですので、ぜひご利用ください。また、機能追加や要望などのissueやpull requestもお待ちしています。

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-accesslog

[^1]: [Labeled Tab Separated Values (LTSV) ノススメ](http://blog.stanaka.org/entry/2013/02/05/214833)
[^2]: http://ltsv.org/
[^3]: [恐らくそれなりに正確なGoのApacheログパーザーを書いた](http://www.songmu.jp/riji/entry/2017-06-24-axslogparser.html)
[^4]: https://github.com/Songmu/postailer
[^5]: [カスタマイズしたグラフを表示する](https://mackerel.io/ja/docs/entry/advanced/advanced-graph)
[^6]: [実験的機能を利用する](https://mackerel.io/ja/docs/entry/advanced/experimental-features-config)
[^7]: [Nginxのアクセスログからステータスコードごとにリクエスト数をサービスメトリックに投稿する](https://mackerel.io/ja/docs/entry/advanced/fluentd#example-nginx)
