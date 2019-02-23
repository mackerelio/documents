---
Title: クラウド移行を契機にMackerelを導入 ゲーム開発者が監視項目を育てる
Category:
- pickup
- customers
Date: 2018-10-29T10:41:08+09:00
URL: https://mackerel.io/ja/blog/entry/customers/segagames
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/10257846132656276503
---

<div class="page-entry-interview">
  <p>Mackerel導入事例 株式会社セガゲームス様<br>
    株式会社セガゲームス <a href="https://sega-games.co.jp/" target='_blank'>https://sega-games.co.jp/</a>
  </p>

  <figure class="first-photo">
    <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104455.jpg" alt="" />
  </figure>

  <ul class="interview-members">
    <li class="member">
      <p class="company">セガゲームス</p>
      <p class="name member-a">日合 亨さん</p>
    </li>
    <li class="member">
      <p class="company">セガゲームス</p>
      <p class="name member-b">津田 哲さん</p>
    </li>
    <li class="member">
      <p class="company">株式会社はてな</p>
      <p class="name member-c">松木 雅幸</p>
    </li>
    <li class="member">
      <p class="company">株式会社はてな</p>
      <p class="name member-d">井上 大輔</p>
    </li>
    <li class="member">
      <p class="company">インタビュー</p>
      <p class="name interviewer">星暁雄（ITジャーナリスト）</p>
    </li>
  </ul>

  <div class="main-text">
    <p class="date-published">記事公開日： 2018年10月29日 · 所属はインタビュー当時のものです</p>
    <p class="introduction">セガゲームスでは、オンラインゲームのインフラとしてパブリッククラウドの利用を開始するにあたり、監視サービスとしてMackerelを導入しました。ゲーム会社として監視サービスに何を求めていたのか。実際に利用してみてどうだったのか。はてなMackerelチームが迫ります。</p>
    <h2>コードを書いてきた開発者がインフラを見るように</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">まず皆さんの自己紹介をお願いします。はてな側から。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">Mackerelのプロダクトマネージャーの松木（id:Songmu:detail / <a href="https://twitter.com/songmu" target="_blank">@songmu</a>）です。プロダクトの仕様策定や今後の方向性を決める仕事に関わっています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">Mackerel CRE（Customer Reliability Engineer）の井上（id:a-know:detail / <a href="https://twitter.com/a_know" target="_blank">@a_know</a> ）です。Mackerelをご利用いただいているお客様との信頼関係構築をミッションにしています。</span>
    </p>
    <p class="dialog dialog-intro">
      <b class="speaker member-a">セガゲームス日合さん（以下、セガ日合）</b>
      <span class="speak speak-intro">セガゲームスの日合です。スマートフォンアプリのプログラムを開発するソフト開発部第2ソフト開発セクションのマネージャーと、パブリッククラウドのサーバー周りの管理や、内製タイトルの負荷試験などを担当するサーバー開発支援課 課長です。2つの課の課長を兼任しています。</span>
    </p>
    <p class="dialog dialog-intro">
      <b class="speaker member-b">セガゲームス津田さん（以下、セガ津田）</b>
      <span class="speak speak-intor">日合と同じくサーバー開発支援課にいる津田です。プログラマ出身ですが、Linux周りの経験があったことから、領域がインフラに広がってきました。DevOpsのOpsの部分、インフラをコードで管理する仕事をしています。</span>
    </p>

    <figure>
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104451.jpg" alt="" />
    </figure>

    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">実は、私は一度もインフラエンジニアの職種に就いていたことがないんです。ですが、最近はインフラ設定などをコード化する流れもあり、アプリケーションエンジニアはコードを書くことは得意なので、そういった最近のトレンドとは相性がいいと感じています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">私もインフラ部門で働いた経験はありません。前職ではソーシャルゲームに関わっていたので、インフラ構築の重要さは体験しました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">僕もインフラ専任はやったことがない（笑）。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">全員がコードを書く側の人なのですね。Mackerel導入にあたり、どのような課題、期待があったのでしょうか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">
        <span class="speak-content">オンラインゲームのインフラとして、パブリッククラウドを活用して移行するのに合わせてMackerelを導入しました。時期は2016年10月頃だったと思います。導入の時点で大きく期待していたことは、まず僕ら自身の運用コストを下げることでした。</span>
        <span class="speak-content">モバイルゲームとサーバーの監視は、切っても切れない関係です。従来は、オンプレミスのサーバー環境を利用し、監視のためにオープンソースソフトウェアのツールであるZabbixを使っていたのですが、タイトルの種類が増えていくとどんどん特定個人の負荷が増えていく。特定個人とはここにいる津田のことですが（笑）。このコストを抑えたかった。</span>
      </span>
    </p>

    <figure class="float-right figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104530.jpg" alt="" />
      <figcaption>日合 亨さん</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-a view-sp">セガ日合</b>
      <span class="speak">それに加えて、僕たちの会社が作るタイトルであれば運用も僕たち自身で責任を持ってがんばればいいのですが、他の会社さんが作ったタイトルの場合、コードの中身にまで干渉するのは難しい。干渉せずに監視できるやり方が求められていました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">
        <span class="speak-content">運用の都合でサービスを止めてはいけない。問い合わせが来るのが第一報になってはいけない。健全な状態をモニタリングする必要があります。そこで監視ツールは重要な役割を果たします。</span>
        <span class="speak-content">タイトルの種類が増えてくると、Zabbixの環境をそれぞれのタイトルごとに量産することになりますが、タイトルによって負荷のかかり方も大きく違います。そこでどうしても、監視で見るべき項目が変わり、カスタマイズが必要です。Zabbixの環境が不揃いになって管理の負担が増えてしまいます。さらに監視ツールのためのサーバーの面倒も見ないといけない。</span>
      </span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">オンラインゲームの場合、ユーザー数が急激に拡大しても、オンプレミスだとサーバー調達が間に合いません。パブリッククラウドは柔軟にピークに合わせてスケールできるメリットがありますね。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">負荷変動が激しいということですが、負荷のピーク時に問題が起きたりしませんでしたか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">そこは、Mackerelは問題なく動いてくれました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">パブリッククラウドに対しては、これから慣れていこう、ノウハウを貯めていこうというところがあり、負荷に応じて自動的にスケールするオートスケールは現時点では取り入れていません。ある程度は手動でサーバーを増やしたり減らしたりしています。Mackerelは、それを意識する必要なく柔軟に対応してくれます。そのため運用の工数はかなり削減できました。</span>
    </p>
    <h2>ゲーム開発者フレンドリーなMackerelで、監視を育てる</h2>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">ゲーム特有の監視項目はありますか？　ゲーム内イベントへのユーザーの参加率などのような。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">タイトルのKPI（重要業績評価指標）に関しては、Mackerelなどではなく、別のシステムを使っています。とはいえ、登録者数、DAU、そのほかログから集計する数値など、アプリ固有のデータのグラフは作っています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">オンラインゲームでは、イベント期間だけ走らせるSQLクエリが出てきます。例えばランキングを見るなどのクエリです。そこにボトルネックがないか、他の値との相関を見たいので、そこにMackerelを使って監視しています。</span>
    </p>

    <figure class="float-left figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104527.jpg" alt="" />
      <figcaption>津田 哲さん</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">Mackerelの導入に際しては、開発者が簡単に監視項目を追加できることが選定のポイントとして挙がっていました。ゲームを一番よく知っているのは作った本人たちですので。それにもうひとつ、日本語のサポートが最も充実していたツールがMackerelでした（笑）。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">必要な場合には、Mackerelでは独自プラグインを簡単に書けることを特徴としています。開発言語も、シェルでも、Perlでも、Pythonでも、工夫すればJavaでも使える。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">私たちの場合、まだアプリケーション開発者がプラグインを書く段階にまでには至っていません。ただ、アプリケーションから直接Web APIを叩いてグラフを作っているプロジェクトはあります。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">それも、利用形態としては進んでいるパターンといえます。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">監視項目をアドホックに取る場合もありますか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">人によります。「夜中にたたき起こされたくない」意識が強いプログラマだと、「コードのこのへんに負荷があるんじゃないか」と思うと、予防的に監視を仕込んでいく場合もあります。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">そのような利用方法は、ツールの狙いと合っています。それに関連して、私たちは「監視を育てていってください」とお伝えしています。インフラの特性に合わせて、どういった項目をモニタリングしておくことでサービスの状態を把握できるか、を考えて、設定項目やグラフを追加していく使い方を提案しています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">私たちが最近よく言っているのは「テスト駆動開発に近い」イメージです。今は開発をしながら同時にテストコードも書き、JenkinsのようなCI（継続的インテグレーション）で回して継続的に改善していく流れがあります。それと同様に、監視を育てて改善していく形をお薦めしています。不安に思ったところに監視項目を入れ、運用する中で徐々に改善・成長させていく。開発と運用が一体となってビジネスをしていく以上、インクリメンタルに改善していくことが大事だと考えています。</span>
    </p>
    <h2>インフラとミドルウェアをきれいに分離できる</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">Mackerelと他のツールを比べて、ひと味ちがう部分は？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">
        <span class="speak-content">Zabbixだと、値を取るためにエージェント側になんらかのスクリプトを仕込む必要があります。つまり実行環境にプログラムを置く必要がある。Mackerelは、Web APIで簡単に値を取れる。ミドルウェア（監視ツール）とインフラを切り離せます。</span>
        <span class="speak-content">ミドルウェアより下のレイヤーの場合、標準的なプラグインが公式でサポートされているので、まずそれを使いましょうという形になっている。これがZabbixの場合、例えばMySQLを監視するとき、値を取るやり方がプロジェクトごとに微妙な差異が出やすい。メンテナンス性が悪くなる。標準的なものに自分たちが合わせる方が使いやすいんですね。</span>
      </span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">津田さんは、Mackerel導入前はZabbixを使いこなしていたと思います。その観点で、Mackerelに足りない点はありますか？</span>
    </p>

    <figure class="float-left figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104524.jpg" alt="" />
      <figcaption>松木 雅幸</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">Zabbixは、例えば「ログを見て遅い処理トップ10を抽出して保存する」など、アプリケーション開発者からの細かい要望に対応することができます。ただし、何でもかんでも保存していると、Zabbix側のDBの負荷が高くなっていく。そのような細かい機能がない、という選択はある意味では正しいと感じます。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">Zabbixで「できていたこと」ができなくなる、というデメリットを上回るメリットがMackerelにはありました。従来は、手動で集計したり定期的にスクリプトを動かして取得できるような情報もZabbixで肩代わりさせていた側面がありました。そのような部分は、それほど多くありません。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">必要なのにMackerelでできない……ということがあったら、ぜひサポート窓口にお問い合わせください！</span>
    </p>
    <h2>インフラを動的に扱えることは「快感」</h2>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">津田さんもアプリケーションエンジニアの立場からインフラ管理の立場に変わった訳ですが、「インフラを動的に扱える快感」ってありますよね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">はい！</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">そういうのが分かってくると、パブリッククラウドを動的に使いこなせるようになってきませんか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">残念ながら、その前にあきらめてしまう人が多いですね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">クラウドの時代なので、Infrastructure as a Codeを使いこなすことについても発信してきたいと思っています。なかなか思う通りには進められていませんが。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">「グラフがきれいに引けている」という点だけでも、単純にニヤニヤしてしまいます。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">笑顔ですね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">インフラは、ある瞬間を触っただけでは、何が起きたか分かりません。改善したのかどうかも、把握しづらい。一方、プログラムは実行するとエラーが出たり、動いたりする。フィードバックがすぐに来ます。インフラをモニタリングすることで、サーバー設定変更などのフィードバックを得られやすくすることは、プログラミングの楽しさと似た部分があると思います。</span>
    </p>

    <figure class="float-right figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104521.jpg" alt="" />
      <figcaption>井上 大輔</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">以前は、プログラマがうんと時間がかかるクエリを書くと、インフラ担当者から「こんなグラフになっている」と怒られることがよくありました。Mackerelでは、そこを自分で見られるようになる。今のクエリでこういう影響が起きるんだと把握できて、「自分ごと」にしやすくなる。アプリケーションエンジニアからすると、それまでインフラは遠いものと感じていたのが、今ではコードとして扱えるようになった。一種の全能感が得られるんじゃないかと思います。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">僕自身の実感でいうと、パブリッククラウドは何回かクリックするだけで設定できてしまう。だから間違えたら「リセットしちゃおう」といった発想になる。オンプレミスの場合、まず線を引いて、試行錯誤をするにはそれをさらに組み直す必要がある。クラウドだと、図面ではなく現実のシステムで試行錯誤ができてしまいます。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">はてなでは、社内インフラで動的にサーバーを増やしたり減らしたりする中で、Mackerelを内製してきました。今、社内ではオンプレミスとパブリッククラウドを併用していますが、その両方にMackerelを適用できるところに特徴があります。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">オンプレミスの段階でMackerelを入れておくと、いざ「その環境をクラウドに移行しよう」とタイミングで、それまでに蓄積されたグラフが真価を発揮します。キャパシティプランニングの際にも、Mackerelでは長期間にわたる時系列データを残せるので、今までの状況を知るときにその機能が生きます。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">Mackerelには、アノテーション、メモを残せる機能がありますよね。あれをZabbixで同様にやろうとすると、スクリーンショットをとったり、記憶を頼りに探したりすることになります。また、作られた時代が違うということもありますが、MackerelはUIが軽い。時系列データを見るときにグラフが連動してくれます。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">同じ種類のサーバーを複数台並べたときのオペレーションは、Zabbixでは大変ですね。特にCPU負荷を、複数ホストをまとめて項目にするようなスクリプトはなかなか書けない。Mackerelは「ロール」を切るだけでできる。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">サーバー台数を増やすとき、従来だと、開発者がインフラ担当チームに「これを追加でお願いします」と申請するやり方になりがちです。Mackerelは、ロールにサーバーが追加される場合、自動的に監視が入ります。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">
        <span class="speak-content">パブリッククラウドでは、使った分お金がかかることを忘れがちです。エンジニアに「自由に考えて」と言ったら、1台分で済むところを2～3台分使おうと考えていたりする。そこで全体感をもって管理できるツールがあると便利です。</span>
        <span class="speak-content">例えば、複数のサーバーを使って可用性を高めて運用する場合があります。クリティカルではないサーバーが1〜2台落ちても、問題がないように構成します。実際にサーバーが落ちたときにアラートは出すけれども、それをいちいち意識はしたくない。Mackerelのロール機能では、再びサーバーが立ち上がったとき、それが同じサーバーか違うサーバーかは区別なく取り扱ってくれます。「手放し感」が上がって、しなくていい管理は、しなくて済むようになりました。</span>
        <span class="speak-content">インフラ管理では、何かあればアラートが来ます。何が起きたか分からないと調査が必要になって、夜中に叩き起こされる。その時に「こういった症状が出ているが、運用には問題はない」と分かれば、インフラ担当は安心してまた寝られます。</span>
      </span>
    </p>

    <figure>
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104447.jpg" alt="" />
    </figure>

    <h2>選定ではクラウド導入によるコスト削減を可視化</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">海外展開についてはいかがでしょうか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">ワールドワイドで展開するタイトルには、『プロサッカークラブをつくろう！ ロード・トゥ・ワールド』、スマートフォン版『D×2 真・女神転生 リベレーション（英語名：SHIN MEGAMI TENSEI Liberation Dx2）』があり、英語圏と繁体字圏で運営中です。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-d">はてな井上</b>
      <span class="speak">Mackerelのような有償のサーバー監視サービスの導入にあたり、社内手続きで工夫が必要な点はありましたか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">最初のうちは、社内調整に手間取りました。一つの理由はコスト。オープンソースソフトウェアのツールであれば「0円」のところが、商用ツールでは有償になります。なぜ費用が必要なのかを説明しないといけません。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">そこで、オープンソースのZabbixでは実は運用コストがこれだけかかっています、と可視化して。「結果的にMackerel導入でコストは下がります」と説明しました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">Zabbixのサーバーにもそこそこのスペックが必要なので、差し引きでは下がるという形で説明しました。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">セガゲームスのお二人から、Mackerelへの要望をお願いできますか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">セガ日合</b>
      <span class="speak">僕らのゲームのタイトルと、Mackerelというミドルウェアのマッチする度合いが、たまたま今はベストな状態だと考えています。ただし、今後は今までのノウハウでは対処しきれない場合も出てくると思います。例えば、.NETの監視などはこれからの課題です。そうした今後の新しい課題への解決策も含めて、監視に対するトータルなアドバイスも期待しています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">.NET監視についてはよく質問をいただきます。良い事例をオープンにしたり、外部の開発者を巻き込んで監視設計やプラグインを作っていければいいと考えています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">Mackerelも含め、「サーバー監視は何のためにあるのか」という基本的な部分が、初めて触る人にはなかなか理解してもらえない。そうした導入やコンサルティングに関わる部分の支援にも期待しています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">ドキュメントなど情報発信を大事にして、充実させていきたいと考えています。例えばテストコードへの認識は、この10年で大きく向上しました。昔はコードを書けば、後はテスターがテストしてくれるといった認識で動いていたと思いますが、今は開発者がテストコードを書くことが当たり前になりました。監視もそうなるといいなと思っています。リアルタイムな監視はサービスの肝の部分です。それを踏まえて「監視設計を含めてサービスを考えてください」と言っています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">セガ津田</b>
      <span class="speak">グラフ機能では監視対象メトリクスをグラフ化する使い方が中心ですが、そこにKPIも一緒に乗せて、負荷連動でダッシュボードから一元管理できるといいなと思っています。「あるときは数字で表示」「あるときは円グラフで表示」といった形でビジュアルな可視化の機能も充実してくれば、Mackerelを見るだけでいい！という形にできます。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">ダッシュボードの機能改善も、今まさに進めています。早ければ2018年内には新機能をお見せできると思います。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">ありがとうございました。</span>
    </p>

    <figure>
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181019/20181019104443.jpg" alt="" />
    </figure>
  </div>
</div>
