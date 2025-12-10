---
Title: グラフを利用する
Date: 2014-05-01T14:50:06+09:00
URL: https://mackerel.io/ja/docs/entry/howto/view-graphs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815723052554
---

[:contents]

<h2 id="about">グラフの見方</h2>

グラフは大きく2つの部分に分かれています。上部が詳細なビューで、下部はより広範囲の概観になっています。各系列はホストごと、メトリックごとに色分けされます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181446.png" alt="f:id:mackerelio:20150805181446p:plain" title="f:id:mackerelio:20150805181446p:plain" class="hatena-fotolife" itemprop="image"></span></p>

グラフの線にマウスポインタを乗せると、ある時刻におけるリソースの詳細な情報が確認できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181428.png" alt="f:id:mackerelio:20150805181428p:plain" title="f:id:mackerelio:20150805181428p:plain" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="period">グラフの表示期間を変更する</h2>

下部の概観部分はスライダーになっています。ここを操作することで、上部で表示する範囲の移動・拡大縮小を行えます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805182732.gif" alt="f:id:mackerelio:20150805182732g:plain" title="f:id:mackerelio:20150805182732g:plain" class="hatena-fotolife" itemprop="image"></span></p>

以下をクリックすると 30分・1時間・1日といった固定の時間幅でグラフを確認できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906103033.png" alt="f:id:mackerelio:20220906103033p:plain" title="f:id:mackerelio:20220906103033p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

時計のアイコンで期間の指定ができます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906111849.png" alt="f:id:mackerelio:20220906111849p:plain" title="f:id:mackerelio:20220906111849p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<h2 id="select-metrics">グラフに表示するメトリックを選択する</h2>

グラフの下に表示されたメトリック名をクリックすると、そのメトリックの表示・非表示を切り替えられます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906112652.png" alt="f:id:mackerelio:20220906112652p:plain" title="f:id:mackerelio:20220906112652p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

<h2 id="simplify">グラフの簡略表示</h2>

サービス詳細画面のロールグラフでホスト数が多いもの（30ホストより多い）は簡略表示されます。積み上げ表示の場合はsumを、折れ線グラフの場合はmax, min, avgを表示するようになっています。簡略表示されたグラフの詳細が見たい場合はグラフ下の「全てを表示」をクリックすると確認できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160229/20160229152333.png" alt="f:id:mackerelio:20160229152333p:plain" title="f:id:mackerelio:20160229152333p:plain" class="hatena-fotolife" itemprop="image"></span></p>

グラフの詳細画面では200ホスト以上のものは簡略表示されますが、「簡略表示」のチェックボックスから切り替えができます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906103724.png" alt="f:id:mackerelio:20160229152333p:plain" title="f:id:mackerelio:20220906103724p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

<h2 id="graph-annotations">グラフにアノテーションを作る</h2>
任意の情報をグラフに書き残すことができます。グラフの時刻部分をドラッグしてアノテーションの対象となる期間を指定し、タイトルと詳細を記入することで作成できます。

グラフアノテーションは、サービスメトリックのグラフとロールグラフに対して作成することが可能です。サービス単位やロール単位でおこなわれるような、アプリケーションのデプロイやリリースなどに関する情報を書き残しておくと便利です。

![グラフのアノテーション](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170125/20170125184543.png)

<h2 id="full-screen">グラフを全画面表示にする</h2>

グラフ右上の全画面表示ボタンをクリックすると、新しいウィンドウやタブでそのグラフのみを全画面表示にします。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906111329.png" alt="f:id:mackerelio:20220906111329p:plain" title="f:id:mackerelio:20220906111329p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<h2 id="post-to-channel">グラフを通知チャンネルに投稿する</h2>
Mackerel上から直接グラフURLを通知チャンネルに流すことができます。グラフメニューのカメラアイコンをクリックし、グラフURLを送るチャンネルを選択し投稿ボタンを押すとURLがグラフの情報と合わせて投稿されます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906110717.png" alt="f:id:mackerelio:20161007115204p:plain" title="f:id:mackerelio:20220906110717p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20161007/20161007115204.png" alt="f:id:mackerelio:20161007115204p:plain" title="f:id:mackerelio:20161007115204p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

[表示するメトリックの選択](https://mackerel.io/ja/docs/entry/howto/view-graphs#select-metrics) をしている場合はその状態を反映したグラフが共有されます。アラートグラフ、ダッシュボードなどの埋め込みグラフ、式による監視のプレビューグラフは未対応です。

この機能を利用するにはあらかじめ <a href="https://mackerel.io/my/channels/-/create" target="_blank">通知チャンネル</a> を設定する必要があります。現在投稿がサポートされている通知チャンネルは以下の通りです。

* Slack
* Chatwork
* Microsoft Teams

<h2 id="graph-share">グラフを共有する</h2>

さまざまな方法でグラフを他者と共有できます。グラフの共有メニューは、各グラフの共有ボタンをクリックすると表示されます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906110938.png" alt="f:id:mackerelio:20220906110938p:plain" title="f:id:mackerelio:20220906110938p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20180726/20180726163218.png" alt="f:id:mackerelio:20180726163218p:plain" title="f:id:mackerelio:20180726163218p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

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

公開中のグラフの確認や公開停止などは、[「公開中のグラフ」](https://mackerel.io/my?tab=sharedGraphs) メニューから実施してください。

<h2 id="graph-definitions">グラフの表示内容を変更する</h2>

カスタムメトリックのグラフ右上にある歯車のアイコンをクリックするとグラフ定義画面が開きます。この画面ではグラフごとに表示内容の変更ができます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906110444.png" alt="f:id:mackerelio:20220906110444p:plain" title="f:id:mackerelio:20220906110444p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906114113.png" alt="f:id:mackerelio:20180726163218p:plain" title="f:id:mackerelio:20220906114113p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

「このグラフを削除」ボタンでグラフを非表示にできますが、そのグラフのカスタムメトリックが新たに投稿されるとグラフは再度表示されます。

<h2 id="delete-graph">グラフ定義を削除する</h2>

不要なグラフ定義は <a href="https://mackerel.io/my/graph-defs" target="_blank">グラフ定義一覧</a> 画面で削除できます。グラフ定義一覧画面へは、ホスト詳細画面のカスタムメトリックという見出し右側の「設定」からアクセスできます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906114435.png" alt="f:id:mackerelio:20220906114435p:plain" title="f:id:mackerelio:20220906114435p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

なお、グラフ定義はオーガニゼーションが保有する設定です。削除するとすべてのホストに影響がありますのでご注意ください。
