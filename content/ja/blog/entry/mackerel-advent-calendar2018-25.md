---
Title: 色覚サポートテーマ機能とアクセシビリティ
Date: 2018-12-30T02:46:20+09:00
URL: https://mackerel.io/ja/blog/entry/mackerel-advent-calendar2018-25
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/10257846132692156099
---

プロダクトマネージャーの id:Songmu:detail です。遅くなりましたが、この記事は[Mackerel Advent Calendar 2018](https://qiita.com/advent-calendar/2018/mackerel)の最終日の記事です。

Mackerelのいぶし銀的な好機能として「テーマ機能」があります。これはその名の通りテーマを切り替えられる機能で、画面右上の [アカウント設定] - [ユーザーインターフェース] と辿ると設定画面に行き着くことができます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181230/20181230011142.png)

現在以下の4テーマがあります。

- skyscraper
  - 標準の白背景テーマ
- hacker
  - 黒背景のテーマ
- racing
  - 白背景のハイコントラストテーマ
- electricity
  - 黒背景のハイコントラストテーマ

色覚サポート用のハイコントラストテーマを用意しているところもポイントで、テーマ名に下のアイコンがついているものが、ハイコントラストテーマです。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151217/20151217164947.jpg)

例えば、electrictyテーマのグラフは以下のように視認性が高いのが特徴です。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151217/20151217165227.png)

この機能はアクセシビリティ上も重要な機能だと考えています。実際、ハイコントラストテーマは、ユーザーの「グラフの色の判別が難しいことがある」という声を受けて開発したものです。

[https://mackerel.io/ja/blog/entry/2015/12/18/115149:embed]

## テーマ機能の生い立ち

実はテーマ機能自体は遊び心から生まれたものです。元々は2015年のエイプリルフール企画としてほんの数日で作られました。その時の様子がこちらです。

[https://mackerel.io/ja/blog/entry/2015/04/01/125239:embed]

この企画が好評だったことを受け、また、当時のMackerelのプロダクトオーナーであった id:stanaka:detail の黒背景への強いこだわりもあり、テーマを切り替える機能として正式化したのです。

[https://mackerel.io/ja/blog/entry/2015/04/17/120022:embed]

このように最初は単なる遊び心だったのですが、このテーマ機能があったおかげで、その後の色覚サポートテーマの追加が容易にできたことは、これまでの開発の中でも印象深い出来事です。

## アラートの配色と色覚サポート

色覚サポートテーマでは、これまでは主にグラフの配色を意識していましたが、今年の目玉新機能の「[アラートグループ機能](https://mackerel.io/ja/docs/entry/howto/alert-groups)」において、「アラート状態を色で識別させようとすると見づらい人がいるのではないか」とディレクターの id:daiksy:detail が指摘し、シミュレーターを使ってみるなどすると、果たしてそのとおりであったので、アラートの配色を調整することにしました。

この辺りは開発チームのアートディレクターである id:takuwolog:detail に豊富な知見があり、丁寧に検討をしてもらった末に、色覚サポートテーマについては大胆な配色変更をおこないました。具体的には、OKの色を緑ではなく青にしたのです。この辺りの詳細は彼がどこかで解説をしてくれるかもしれません。

## まとめ

その他、テーマ機能に関しては「OSのダークモード連動もやりたいね」とか雑談レベルでチームメンバーと会話したりはしています。

今後もMackerelではこういった点も含めてユーザーの使いやすさにこだわって機能開発をしていきます。来年のMackerelもお楽しみに。

それでは皆様良いお年を！
