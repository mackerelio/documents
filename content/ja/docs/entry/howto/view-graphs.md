---
Title: グラフを利用する
Date: 2014-05-01T14:50:06+09:00
URL: https://mackerel.io/ja/docs/entry/howto/view-graphs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815723052554
---

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140501/20140501144542.png" alt="f:id:mackerelio:20140501144542p:plain" title="f:id:mackerelio:20140501144542p:plain" class="hatena-fotolife" itemprop="image"></span></p>

グラフは大きく2つの部分に分かれています。上部が詳細なビューで、下部はより広範囲の概観になっています。各系列はホストごと、メトリックごとに色分けされます。

下部の概観部分はスライダーになっています。ここを操作することで、上部で表示する範囲の移動・拡大縮小を行えます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140501/20140501150919.gif" alt="f:id:mackerelio:20140501150919g:plain" title="f:id:mackerelio:20140501150919g:plain" class="hatena-fotolife" itemprop="image"></span></p>

グラフの右上には現在の時刻範囲が表示されています。ここをクリックすると、30分・1時間・1日といった、固定の時間幅でグラフを確認できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161205/20161205165430.png" alt="f:id:mackerelio:20140501144608p:plain" title="f:id:mackerelio:20140501144608p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

グラフの線にマウスポインタを乗せると、ある時刻におけるリソースの詳細な情報が確認できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140501/20140501144617.png" alt="f:id:mackerelio:20140501144617p:plain" title="f:id:mackerelio:20140501144617p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<h2 id="simplify">簡略表示</h2>

サービス詳細画面のロールグラフでホスト数が多いもの（30ホストより多い）は簡略表示されます。積み上げ表示の場合はsumを、折れ線グラフの場合はmax, min, avgを表示するようになっています。簡略表示されたグラフの詳細が見たい場合はグラフ下の「全てを表示」をクリックすると確認できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160229/20160229152333.png" alt="f:id:mackerelio:20160229152333p:plain" title="f:id:mackerelio:20160229152333p:plain" class="hatena-fotolife" itemprop="image"></span></p>

グラフの詳細画面では200ホスト以上のものは簡略表示されますが、「簡略表示」のチェックボックスから切り替えができます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160229/20160229153216.png" alt="f:id:mackerelio:20160229153216p:plain" title="f:id:mackerelio:20160229153216p:plain" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="graph-annotations">グラフにアノテーションを作る</h2>
任意の情報をグラフに書き残すことができます。グラフの時刻部分をドラッグしてアノテーションの対象となる期間を指定し、タイトルと詳細を記入することで作成できます。

グラフアノテーションは、サービスメトリックのグラフとロールグラフに対して作成することが可能です。サービス単位やロール単位でおこなわれるような、アプリケーションのデプロイやリリースなどに関する情報を書き残しておくと便利です。

![グラフのアノテーション](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170124/20170124180330.png)

<h2 id="post-to-channel">グラフを通知チャンネルに投稿する</h2>
Mackerel上から直接グラフURLを通知チャンネルに流すことができます。グラフメニューのカメラアイコンをクリックし、グラフURLを送るチャンネルを選択し投稿ボタンを押すとURLがグラフの情報と合わせて投稿されます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20161007/20161007115203.png" alt="f:id:mackerelio:20161007115203p:plain" title="f:id:mackerelio:20161007115203p:plain" class="hatena-fotolife" itemprop="image"></span></p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20161007/20161007115204.png" alt="f:id:mackerelio:20161007115204p:plain" title="f:id:mackerelio:20161007115204p:plain" class="hatena-fotolife" itemprop="image"></span></p>

それぞれのチャンネルは <a href="https://mackerel.io/my/channels?new" target="_blank">通知チャンネル設定</a> から設定が可能です。

アラートグラフ、ダッシュボードなどの埋め込みグラフ、式による監視のプレビューグラフは未対応です。また、現在投稿がサポートされている通知チャンネルは <a href="https://mackerel.io/ja/docs/entry/howto/alerts/slack" target="_blank">Slack</a>, <a href="https://mackerel.io/ja/docs/entry/howto/alerts/hipchat" target="_blank">Hipchat</a>, <a href="https://mackerel.io/ja/docs/entry/howto/alerts/typetalk" target="_blank">Typetalk</a>, <a href="https://mackerel.io/ja/docs/entry/howto/alerts/line" target="_blank">LINE</a>, <a href="https://mackerel.io/ja/docs/entry/howto/alerts/yammer" target="_blank">Yammer</a>, <a href="https://mackerel.io/ja/docs/entry/howto/alerts/microsoft-teams" target="_blank">Microsoft Teams</a>です。


<h2 id="graph-share">グラフを共有する</h2>

さまざまな方法でグラフを他者と共有できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20180726/20180726163218.png" alt="f:id:mackerelio:20180726163218p:plain" title="f:id:mackerelio:20180726163218p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

グラフの共有メニューは、各グラフの共有ボタンをクリックすると表示されます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20180726/20180726163225.png" alt="f:id:mackerelio:20180726163225p:plain" title="f:id:mackerelio:20180726163225p:plain" class="hatena-fotolife" itemprop="image"></span></p>


<h3 id="download-image">グラフをダウンロードする</h2>
`ダウンロード` ボタンを押すことで、グラフ画像をダウンロードできます。画像化されるグラフの表示期間は、コンソール上で表示されている期間と同じです。

<h3 id="share-url">URLを共有する</h2>
グラフを表示させるためのURLを取得できます。このURLを共有することでグラフを共有できます。共有されるグラフの表示期間は、「現在の表示期間」チェックボックスをオンにすることで、コンソール上で表示されている期間と同じものにすることが可能です。そのオーガニゼーションに所属しているアカウントだけが、このURLでグラフを閲覧できます。

<h3 id="embed-graph">埋め込み形式を選択し共有する</h2>
HTMLやmarkdown文書に埋め込める形式で、グラフを共有するための情報を取得できます。選択できる形式は以下の通りです。

- iframe
- 画像
- SVG
- Markdown

そのオーガニゼーションに所属しているアカウントだけが、埋め込まれたグラフを閲覧できます。共有されるグラフの表示期間は、「現在の表示期間」チェックボックスをオンにすることで、コンソール上で表示されている期間と同じものにできます。

<h3 id="publish-graph">グラフを外部に公開する</h2>
「URLを作成」ボタンを押すことで、そのグラフを外部に公開できます。**公開URLを知っている人は誰でもグラフを閲覧可能となりますので、注意してください。**

公開中のグラフの確認や公開停止などは、[「公開中のグラフ」メニュー](https://mackerel.io/my?tab=sharedGraphs)から実施してください。
