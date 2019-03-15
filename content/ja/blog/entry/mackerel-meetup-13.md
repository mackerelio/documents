---
Title: '【開催レポート】Mackerel Meetup #13 を開催しました！'
Category:
- event
Date: 2019-03-06T17:03:37+09:00
URL: https://mackerel.io/ja/blog/entry/mackerel-meetup-13
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/17680117126987480064
---

こんにちは！ Mackerelチーム CREの id:a-know:detail です。今日は、去る3月1日（金）に開催した公式イベント、[Mackerel Meetup #13](https://mackerelio.connpass.com/event/118995/)のレポートをお届けしたいと思います！


[f:id:mackerelio:20190305073910j:plain]

<!-- more -->

13回目となる今回のイベントは、[サイボウズ株式会社](https://cybozu.co.jp/)様の素敵な会場をお借りしての開催となりました！

[f:id:mackerelio:20190305073601j:plain]

日本橋にあるタワービルの27Fフロアの入り口をくぐると、そこにはまさしく "公園" のような光景が広がっていました！ "サイボウ樹（じゅ）パーク" と呼ばれるものらしいですよ。

[f:id:mackerelio:20190305073700j:plain]

平日の昼間からの開催にも関わらず、多くの参加者の方にお越しいただきました！

[f:id:mackerelio:20190306170055j:plain]

窓際にはハンモックもあって、このオフィスが都会のド真ん中にあるってことを、つい忘れてしまいそうです（笑）

<figure class="figure-image figure-image-fotolife" title="ハンモックのあまりの心地よさに至福の表情の id:missasan 。">[f:id:mackerelio:20190305073716j:plain]<figcaption>ハンモックのあまりの心地よさに至福の表情の id:missasan 。</figcaption></figure>

こんな素敵な会場での Mackerel Meetup。否が応でも期待が高まります！このような素晴らしい会場をお貸しくださったサイボウズさま、本当にありがとうございます！！


## 『Mackerel 2019』 〜 株式会社はてな プロダクトマネージャー 松木 雅幸 (id:Songmu:detail / [@Songmu](https://twitter.com/songmu))

そんなこんなで、イベントは始まります。トップバッターは、Mackerel のプロダクトマネージャーである id:Songmu:detail より、『Mackerel 2019』と題した、直近の・そして今後の Mackerel の展望についてのお話しです。


[f:id:mackerelio:20190305073923j:plain]


冒頭からの「いや〜、<b>空前の監視ブームですね！</b>」という言葉で、会場は一体感に包まれました（笑）。そう、[「入門 監視」](https://www.oreilly.co.jp/books/9784873118642/)の出版をきっかけに、"監視" に対する一種のムーブメントが起こりつつあることは、このブログを読んでいただいている方であればおそらく感じているんじゃないかな？と思います。id:Songmu:detail の発表以降も、何度と無くこの書名を耳にすることになりました。まだ読んでない！という方は、ぜひ一度手にとってみていただけたらと思います。


その後の id:Songmu:detail の発表では、「[アンバサダープログラム](https://mackerel.io/ja/blog/entry/ambassador/about)」「最近の主要アップデート」と続いて、「ロードマップ状況」についてもご紹介しました。そのなかでも特に「異常検知」機能については、イベント当日がリリース日、という、いろんな意味での "目玉" リリースとなりました。みなさんはもうすでに試されましたでしょうか？　現在はパブリックベータという位置づけで、無料キャンペーン中ですので、ぜひ一度お試しください！



[https://mackerel.io/ja/blog/entry/anomaly-detection-for-roles/about:embed:cite]




その後の発表は「今年のMackerel」として、おそらく多くの方が気にしているであろう「Mackerelのコンテナ環境への対応」や、Mackerelの根幹である「時系列データベースの強化」といったところにまで話が及びました。こうしたところの詳細は、以下の資料にすべて掲載されています。ぜひ確認してみてください！




[https://speakerdeck.com/mackerelio/mackerel-2019:embed:cite]




## 『Mackerelコンテナエージェントによるコンテナ監視について』 〜 株式会社はてな SRE 今井 隼人 (id:hayajo_77:detail / [@hayajo](https://twitter.com/hayajo))

続いての発表も Mackerel チームから。先日 Mackerel の[コンテナ対応としてパブリックベータリリース](https://mackerel.io/ja/blog/entry/weekly/20190218)をした、[mackerel-container-agent](https://github.com/mackerelio/mackerel-container-agent)についてのお話しです。発表をしてくれた id:hayajo_77:detail は、先日大きな反響を呼んだ [https://employment.en-japan.com/engineerhub/entry/2019/02/05/103000:title] という記事も執筆した、はてな でのコンテナ技術の第一人者です。


[f:id:mackerelio:20190305074142j:plain]


話は「mackerel-container-agent とはそもそも何か」といったところから始まり、その概要から、やること/やらないこと、サイドカーパターンを採用した理由、などの話をしたのちに、「では、監視をおこなうためのメトリックはどのように取得すべきか？」といったところにまで及びました。オーケストレーションサービスやネットワークモードごとに三者三様のその内容は、大変聴き応えのある内容でした。


一度聴いただけではすべてを把握することは難しかったと思います、以下の資料をもとに、もう一度確認してみてもらえたらと思います。



[https://speakerdeck.com/hayajo/mackerel-meetup-number-13-tokyo:embed:cite]



mackerel-container-agent は、パブリックベータ中であるということからもおわかりいただけるかと思いますが、まだまだ世に生まれたばかりのソフトウェアです。今回の発表の中でも「展望と構想」として今後取り組んでいきたいことについても言及していますが、実際に試された方からのフィードバックも大いに参考にしています。ぜひみなさんも、この開発に参加していただきたいと思います！




[https://github.com/mackerelio/mackerel-container-agent:embed:cite]




## 『BASEにおけるMackerel利用上の工夫と困りごとのご紹介』 〜 BASE株式会社 SRE 山根 史嵩 様([@fumikony](https://twitter.com/fumikony))

10分ほどの休憩を挟んだのちに、今度はユーザー企業さまからの発表パートです。まずは、[BASE株式会社](https://binc.jp/)の山根様からの発表です。


タイトルに "困りごと" とあったので、「何かご不便な状態が続いていて、それに私達が気づけていなかったのだろうか...？」と少し心配がよぎりましたが、いざ蓋を開けてみたら、「 `/health` エンドポイントパターンとMackerelの外形監視を組み合わせた効果的な監視を実現していること」といった「入門 監視」にも書かれているような "監視のベストプラクティス" と呼べるようなものを実現しているというお話や、「コード化した監視ルールをバックアップ的に活用していること」など、Mackerelの素晴らしい活用方法・事例ばかり！　発表を聴いていたMackerelチーム一同も、歓喜の渦に包まれる発表内容でした！


"困りごと" とされていた部分も、その多くはすでに私達も認識しているような・id:Songmu:detail からの発表にあった "開発ロードマップ" にも予定をしているようなものでしたので、もちろん現時点ではご不便なこともあるかもしれませんが、今後の Mackerel の進化によりきっとその不便は解消・軽減されるものと思います。


聞けば、こうした技術イベントでの登壇は今日が初めてだったとのこと。全くそんなふうには感じない、とても安定感のある発表だったと思います。発表資料は以下のものになります。ご登壇くださり、ありがとうございました！



[https://speakerdeck.com/fumikony/mackerel-meetup-number-13-tokyo:embed:cite]




## 『mackerel-container-agentを用いたマルチテナントKubernetesのモニタリング』 〜 株式会社サイバーエージェント 後藤 秀信様
そしてトリを飾っていただいたのが、[株式会社サイバーエージェント](https://www.cyberagent.co.jp/)の後藤様からの発表。


詳しくは資料を確認していただきたいのですが、**Kubernetes (OpenShift) on OpenStack on Kubernetes**（Kuberenetes上に構築された OpenStack 基盤の、**さらにその上に構築された Kubernetes 環境**）を、mackerel-container-agent を使ってどのようにモニタリングするか？」というお話しで、発表を聴いていた はてな社員も思わず息を呑むような環境についてのお話しでした。


後藤様自身「ミルフィーユのような」と表現されたような環境に対して、どのようにして mackerel-container-agent を導入するか？その方法とメリット/デメリット、といったようなところを、ひとつひとつ奥深いところまで、それでいて丁寧に、解説をしていただきました。


それに関連して MutatingAdmissionWebhook への対応要望などもいただいて、さっそく id:hayajo_77:detail はやる気に燃えていました（笑）。やはりこれだけの大規模環境でしっかりと使っていただけるプロダクトである、ということは、我々にとってもやる気に繋がります。逆に、サイバーエージェント様のような大規模環境でも柔軟に使っていただけるプロダクトでもあるということだと思っていますので、監視基盤にお悩みの方は、ぜひ今回の資料を確認していただき、加えて[Mackerelサポートチーム](mailto:support@mackerel.io)にもご相談いただけたら、と思います！




[https://speakerdeck.com/goto_hidenobu/mackerel-container-agentwoyong-itamarutitenantokubernetesfalsemonitaringu:embed:cite]




## そして最後は懇親会！

<figure class="figure-image figure-image-fotolife" title="Mackerel プロデューサーの id:nkako から、乾杯のご挨拶！">[f:id:mackerelio:20190305074448j:plain]<figcaption>Mackerel プロデューサーの id:nkako から、乾杯のご挨拶！</figcaption></figure>

非常に密度の濃い4つの発表を聴いたあとは、サイボウズさんの "サイボウ樹パーク" で懇親会です！今回もささやかではありますが、食べ物・飲み物を用意させていただきました。


[f:id:mackerelio:20190305074553j:plain]


発表内容についてのお話しや、ちょうどこの日にリリースされた異常検知機能についてのお話しで、あちこちで花が咲いていました！


[f:id:mackerelio:20190305074608j:plain]



懇親会ではLTの発表もしていただきました！


[f:id:mackerelio:20190305074707j:plain]


[f:id:mackerelio:20190305074721j:plain]


[f:id:mackerelio:20190305074733j:plain]


以下が、LTでの発表資料です。（掲載がまだの方は、発表資料をご連絡いただき次第、更新しますので、お知らせください！）


[https://speakerdeck.com/fujiwara3/mackerel-meetup-13:embed:cite]






[https://speakerdeck.com/hgsgtk/health-endpoint-pattern-as-application-monitoring-number-mackerelio:embed:cite]




今回のイベントは少し早めの時間に開催させていただいたこともあり、比較的ゆっくりと、お食事やお話しを楽しんでいただけたかな？と思っています。（それでもまだ話し足りない！という方はおられたかと思いますが...（笑））Mackerelチーム一同も、大変楽しい時間を過ごさせていただきました。ご参加いただいた皆様、そして素敵な会場をお貸しくださったサイボウズ株式会社の皆様、本当にありがとうございました！！


## 次回は秋頃の開催予定です！
以上、 Mackerel Meetup #13 の開催レポートをお届けしました。読んでくださった方々に、イベントの雰囲気が少しでも伝わっていれば幸いです。


次回のミートアップイベントは秋頃を予定しています。なんと今年は Mackerel の正式リリースから5周年、という、記念の年です！そういった意味でも、ぜひ期待してお待ちいただけたらと思います！


<figure class="figure-image figure-image-fotolife" title="秋頃にまたお会いしましょう！">[f:id:mackerelio:20190305074839j:plain]<figcaption>秋頃にまたお会いしましょう！</figcaption></figure>
