---
Title: クラウド環境の1000VMにMackerelを導入、企業ユーザーへの障害通知を自動化
Category:
- pickup
- customers
Date: 2018-12-13T17:30:00+09:00
URL: https://mackerel.io/ja/blog/entry/customers/itechankyuhanshin
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/10257846132684423853
---

<div class="page-entry-interview">
  <p>Mackerel導入事例 アイテック阪急阪神様<br>
    アイテック阪急阪神株式会社 <a href="https://itec.hankyu-hanshin.co.jp/" target='_blank'>https://itec.hankyu-hanshin.co.jp/</a>
  </p>

  <figure class="first-photo">
    <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181212/20181212175922.jpg" alt="20181212175922">
  </figure>

  <div class="interview-members-b">
    <div class="members-b-container">
      <div class="members-inner">
        <div class="member">
          <p class="company">アイテック阪急阪神</p>
          <p class="position">マルチメディア事業本部 IPソリューション部 ネットワーク技術課長</p>
          <p class="name member-a">奥 裕之さん</p>
        </div>
        <div class="member">
          <p class="company">アイテック阪急阪神</p>
          <p class="position">マルチメディア事業本部 IPソリューション部 ネットワーク技術課 主事</p>
          <p class="name member-b">森本 健一さん</p>
        </div>
      </div>
      <div class="members-inner">
        <div class="member">
          <p class="company">はてな</p>
          <p class="position">チーフエンジニア、Mackerelプロダクトマネージャー</p>
          <p class="name member-c">松木 雅幸</p>
        </div>
        <div class="member">
          <p class="company">はてな</p>
          <p class="position">サービス・システム開発本部 Mackerelチーム ビジネスマネージャー</p>
          <p class="name member-e">加古 直己</p>
        </div>
      </div>
    </div>
    <div class="members-b-container">
      <div class="members-inner">
        <div class="member">
          <p class="company">インタビュアー</p>
          <p class="position">ITジャーナリスト</p>
          <p class="name interviewer">星 暁雄</p>
        </div>
      </div>
    </div>
  </div>

  <div class="main-text">
    <p class="date-published">記事公開日： 2018年12月13日 · 所属はインタビュー当時のものです</p>
    <p class="introduction">アイテック阪急阪神は、企業ユーザー向けにクラウドサービスを提供しています。サービスの障害発生時に正確かつ自動的にユーザー通知を届けられるツールを求めた結果、Mackerelを採用しました。エージェント導入だけで利用でき、カスタマイズが容易なMackerelの性質が役に立ったということです。導入検討から運用まで、はてなMackerelチームが話を聞きました。</p>
    <h2>顧客に対して自動的にメールを送りたかった</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">まずは皆さんの自己紹介をお願いします。</span>
    </p>
    <p class="dialog dialog-intro">
      <b class="speaker member-a">アイテック阪急阪神 奥さん（以下、アイテック奥）</b>
      <span class="speak speak-intro">マルチメディア事業本部、ネットワーク技術課の課長をしています。インターネット関連、クラウドやサーバーの部門なので、ネットワークやサーバーのエンジニアは課に集結しています。フルマネージドクラウドという名前で、設計から構築までぜんぶ引き受けるサービスを提供しています。</span>
    </p>
    <p class="dialog dialog-intro">
      <b class="speaker member-b">アイテック阪急阪神 森本さん（以下、アイテック森本）</b>
      <span class="speak speak-intor">そのネットワーク技術課で主にサーバーエンジニアとしてやってきました。ホスティングのサーバー基盤、関連するストレージやネットワークも見ています。機器を買ってきて、セットアップして、OSやミドルウェアが動きます、というところまでを担当しています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">Mackerelのプロダクトマネージャーをしています。プロダクトの仕様や方向性を決めています。アイテック阪急阪神さんのような歴史があるSI企業がMackerelを大規模導入してくださって嬉しく思っています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-e">はてな加古</b>
      <span class="speak">Mackerelのビジネスマネージャーです。肩書の通りビジネス面を担当しています。</span>
    </p>

    <figure>
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181212/20181212175915.jpg" alt="20181212175915">
    </figure>

    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">さっそくですがMackerel導入のきっかけを教えていただけますか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">2年ほど前に、初めてMackerelを知りました。その頃は監視システムに特別な問題は起こっておらず導入は見送っています。
        <span class="speak-content">Mackerel導入以前の状況ですが、監視ツールとしてオープンソースのZabbixも使っていましたし、Pandora FMS、Cacti、GrowthForecastなど各種ツールを試していました。ただ、情報があちこちに散らばっているのは分かりにくい。新しいスタッフが入ってきて、複数のツールに情報が散らばっているのを把握するまでが大変でした。</span>
        <span class="speak-content">とどめを刺したのが障害です。2018年3月頃に、我々が管理しているクラウドで大規模障害が起きたのですが、その際に監視サーバーからアラートが正常に飛ばないという出来事がありました。監視ツールを動かすためのサーバーのリソースが障害で尽きてしまっていて、うまく動作しなかった。手動で障害が起きたサーバーを拾い上げ、お客様に連絡を回すのに相当の時間を要しました。</span>
        <span class="speak-content">監視ツールで検知できないと、連絡に時間かかかり関係者に大きな迷惑をかけてしまう。ここをなんとかできないか。そう考えたことが、監視システムの刷新を考えたきっかけでした。</span>
      </span>
    </p>
    <h2>アラートが「オオカミ少年」になるのを避けたかった</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">導入時点では、どのようなことを考えてらっしゃいましたか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">監視の仕組みをどうすればいいかを課の中で議論しました。例えば、社内では営業部門や本部長、それに社外のお客様への連絡が遅くなるのは困る。通知をいかに早く届けるかが重要です。単純に考えると、監視システムにメールを組み込めばいいように思えるのですが、実際はそうなっていませんでした。
        <span class="speak-content">私たちが提供するフルマネージドサービスでは、最終的にアプリケーションを作るのはお客様です。メンテナンスをするのもお客様。そのため、どのようなエラーをどこまでお客様に通知するのかの見極めができておらず、通知をメールで送る形になっていませんでした。</span>
      </span>
    </p>

    <figure class="float-right figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181212/20181212175908.jpg" alt="20181212175908">
      <figcaption>奥 裕之さん</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-a">アイテック奥</b>
      <span class="speak">アラートを送る場合も、気をつけないと対処する必要がないブラフのようなアラートを送ってしまいます。そのすべてにお客様から問い合わせが来るのでは仕事にならない。サービスに影響が出るような事態ではアラートを送りたいが、一方で必要がないアラートは送りたくないと考えていました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">たいしたことがないアラートばかりだと「オオカミ少年」のようになってしまいますね。以前は通知するか否かは人力で判断していたのですか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">そうです。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">アイテック奥</b>
      <span class="speak">運用規定書を提供していたのですが、お客様によっては「細かく連絡が欲しい」と要望をいただくこともあり、温度感がまちまちでした。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">Mackerelを使うことで課題を解決できそうだった訳ですね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">機能的にもMackerelなら通知も含めて解決できそうだと考え、選択しました。障害時にお客様に通知する仕組みを「2カ月で作って欲しい」と言われていましたので、時間的にもMackerelなら実現できそうだと考えました。
        <span class="speak-content">Mackerelでは、サーバー上のエージェントが、Mackerelのサービスと通信して情報を収集します。サーバーで障害が起きたらMackerelのサービスから通知が飛んでくる。サーバー上で動く監視ツールと違って、サーバー側リソースがほぼ食い尽くされているような状態でも大丈夫です。ホストが完全にダメになった場合もアラートを出せます。</span>
        <span class="speak-content">人手による判断を経ず、メールはリアルタイムに届きます。私たちの使い方では、Mackerelのメールを直接お客様に送信するのではなく、一度サーバーで受けて、fromフィールドや本文を私たちのお客様向けに書き換えています。</span>
      </span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">この取り組みでは自動的に通知を送ることが重要だったのですね。書き換えるのは、お客様向けに変更が必要だったからですか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">そうです。メールはMackerelからではなく、私たちのサポートから届く形にしています。お客様がMackerel宛てに返信しないようにするためです。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">内部の独自監視では異常を捕捉しきれないので、外部の監視ツールを使うという形ですね。そうするとアラートの通知はMackerelがやってくれるのですが、メールを加工したいニーズがあった訳ですね。MackerelのメールだとHTMLメールでグラフを表示したりしますが、もっとシンプルにしたかったということでしょうか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">アイテック奥</b>
      <span class="speak">フルマネージドのサービスを提供していて、基本的にお客様は我々に任せきりです。そのため、シンプルなメール文面で通知するようにしています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-e">はてな加古</b>
      <span class="speak">私たちもSaaSの提供者として、どうしても作り手目線になっているところがありました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">そうしたサービスの再提供まで考えると、Mackerelとして公式でメールのテンプレート機能があるといいわけですね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">そうですね。</span>
    </p>
    <h2>潜在的な対象サーバーは1000VM規模</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">Mackerelの導入規模どのくらいなのでしょうか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">現在、Mackerelによる監視を導入したサーバーは450ぐらいですが、導入すべきサーバーは潜在的に1000VM（仮想マシン）ぐらいのニーズがあります。毎年、VM数は増えていますから、今後規模は上がっていくと思います。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">Mackerelによる監視をお求めのお客様には、行き渡っている形でしょうか。</span>
    </p>

    <figure class="float-left figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181212/20181212175859.jpg" alt="20181212175859">
      <figcaption>森本 健一さん</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">そうですね。ほぼ導入済みです。Mackerelのエージェントを入れただけだと最低限の死活監視になりますが、今後は、従来Pandora FMSで行っていたURL監視などを導入していきます。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">Mackerel導入までの検討は、どのように進められましたか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">サービスの概要はMackerelを知った2年前に確認していました。先ほどお話しした大規模障害の後に、トライアル版を導入して本格的に検証しました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-e">はてな加古</b>
      <span class="speak">無償トライアル期間を設けているので、その間に細かく検証いただけるようにしています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">アイテック奥</b>
      <span class="speak">冒頭でもお話した通り、導入の動機は「大規模障害があった時にMackerelで通知してもらおう」なんです。今でも小規模な障害は従来のやり方で対処していますが、大規模障害となると人手で連絡していたのでは間に合わない。そこでまずお客様に通知しよう、ということで導入しました。通知が来た場合は障害対応のページをご確認いただいています。通知がないと、都度こちらに連絡が入って対応しきれないので。森本から「我々の思い描く仕組みを実装するにはMackereが適している」という意見をもらいました。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">大規模障害の検知が最初の目的ということですが、それ以外の使い道も考えていますか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">はい。細かな設定をすることで監視できるので、いろいろ活用できそうだと考えています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-e">はてな加古</b>
      <span class="speak">Mackerelがクラウドサービスであることについての懸念や不安などの意見は出たりしましたか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">MackerelがAWS（Amazon Web Services）上に実装されていることは知っていました。今はクラウド活用がどんどん進んでいるので、基盤として見た場合にAWSならば大丈夫だろうと判断しました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">官公庁や製造業などでもクラウド対応は進んできました。世の中のセキュリティ基準を満たそうとすると、自社基盤よりAWSの方がいろいろな認定を受けていて早く作れる側面もあります。</span>
    </p>
    <h2>MackerelのAPIを使いこなし独自機能を実装、後に公式が取り入れる</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">Mackerelの特徴のひとつが可視化ですが、今回の導入ではそこは有効でしたか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">MackerelにはAPIがあって、障害中のサーバーを拾い出すことができます。それを使って簡単に実装できました。障害を起こしているサーバーを表示する画面をお客様に公開して、通知メールが来ていない場合でも、このページを見れば障害の有無が分かるようにしています。これは定期的に自動処理で画面を作っているので、人手はかかっていません。</span>
    </p>

    <figure class="float-right figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181212/20181212175912.jpg" alt="20181212175912">
      <figcaption>松木 雅幸</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">「オーガニゼーション一覧」の表示機能を開発していると聞いていますが、そこはまた別ですか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">はい、別です。オーガニゼーション一覧は、私たちが構築した独自ダッシュボードで使っています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">独自ダッシュボードを見せて頂いたのですが、非常によく作られていて、私たちのチームメンバーも「この機能は標準であった方がいいよね」という話になりました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">あの2カ月後ぐらいですよね</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">はい、似た機能を作ってリリースしています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">それをすぐプロダクトに反映する早さは良かったですよね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">8月にインターン生が来ていて、「お題」としてやってもらうといいんじゃないかと思って、作ってもらったんです。それをチームでしっかりレビューした後に公式機能としてリリースしました。
        <span class="speak-content">他にも、もしご要望があれば取り込むことを検討していきます。</span>
      </span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">独自ダッシュボードのお話は、監視対象の会社数が多いので、そのための画面を独自に作ったというお話なのですね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">数十社のお客様のサーバーを監視しているのですが、1社ごとに監視内容を見ていきたいというニーズがあり、独自に機能を作りました。別の会社の障害で通知メールが飛んでしまうことをオーガニゼーションを分けることで防ぎたかったので。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">Mackerelは、はてな社内のツールをサービス化したものなので、当初はひとつの会社でひとつのオーガニゼーションを使うことを想定していました。はてなの場合、ひとつのオーガニゼーションで何千台というサーバーを運用しています。
        <span class="speak-content">一方、お客様によって違うパターンもあり、御社のように顧客別にオーガニゼーションを作るパターンもあれば、ゲーム制作会社様がゲームタイトルごとに分けるパターンもありました。当初僕たちが思っていたのと違う運用があり得ることが分かってきました。そこで、Mackerelでもオーガニゼーション一覧のページを公式に作ることにしたのです。</span>
        <span class="speak-content">最近は、自分たちの会社の中でもオーガニゼーションを分けた方がいいんじゃないか、という議論があります。DevOps的なチーム分割の側面からするとそれが自然ですし、実際問題、障害で別のサービス担当者に通知が飛んだりすると怖いので。</span>
      </span>
    </p>
    <h2>Mackerelを開発チームにも使ってもらう</h2>

    <figure class="float-right figure-portrait">
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181212/20181212175904.jpg" alt="20181212175904">
      <figcaption>加古 直己</figcaption>
    </figure>

    <p class="dialog">
      <b class="speaker member-e">はてな加古</b>
      <span class="speak">現状では、コネクティビティの通知だけをしているんですよね。ただ、サービスを拡大する中で、最終的にはMackerelをお客様に提供する可能性はありますか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">お客様に直接触ってもらうことはしていませんが、社内の開発部隊にはRO（リードオンリー）のユーザーを渡して、メモリーやCPUの監視機能を共有しています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-e">はてな加古</b>
      <span class="speak">Mackerel側の考え方としては、アプリケーションのユーザーにも使ってもらうといいんです。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">例えば自分たちのプロダクトに対するプラグインなどは、アプリケーションを作っている人でないと作れない側面があります。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">Mackerelはアプリケーション開発者に使ってもらいたい、という話を座談会のたびにしていますね。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">MackerelはDevOpsの中核でありたいと思っています。開発と運用に断絶があるとうまくいかない。インフラの人だけが見る画面にはしたくなくて、開発の人も見てアクションを取りましょうと。そういうインクリメンタルな改善ができるサービスでありたいと思っています。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">Mackerelへのご要望はありますか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">ひとつは、さきほど挙げたメールのテンプレートがあると嬉しいというのがあります。あと、「一週間単位」とか「9月1日から1カ月」を見るみたいな機能がない。そういう機能があると、サーバー稼働状態の報告書を作る業務が楽になります。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">グラフの表示期間をもっと細かく制御したいというお話ですね。そこのUIは僕たちは「グラフコントローラー」と呼んでいます。たしかにここは、カレンダーから指定するなどもっと多彩な操作ができるように改善したいと考えているところです。来週には、カスタムダッシュボードのリニューアルをリリースする予定なのですが、そこにもこのグラフコントローラーのUIがあります。そうなると同様の要望は多く出てくるのではないかと予想しているので、次はそこの改善に手を付けたいとは考えています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">監視ツールとして、Zabbixも残すつもりです。どこまでMackerelに移すか、と考えたとき、外形監視が今はhttpとhttpsだけなんですが、特定のポート監視ができると、より注力できるかなと。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">あとはICMPやpingとかですね。特定のポートを叩いて監視できれば。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">外からICMP監視ができるといいですね。滅多に落ちませんが、上位ルータがダウンすると広範囲にわたる障害となりますので。外側からルータの監視ができたら嬉しいと思います。今は別の業者に監視をやってもらっているんですが。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">インターネットに露出したポートの監視の機能を追加することは、可能性としてはあります。</span>
    </p>
    <h2>企業ユーザー目線では、カスタマイズ容易性と分かりやすさを評価</h2>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">エンタープライズシステム（企業情報システム）を支える観点から見たMackerelの使い勝手はいかがでしたか？</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">アイテック奥</b>
      <span class="speak">本当に使いたいモノは、作り込む必要がある。Mackerelは他のツールに比べて作りやすいと感じています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">作りやすいし、画面が分かりやすい。新しく入ってきたスタッフにもすぐ把握してもらえる。分かりやすさは重要です。（複数のVMを）ロールでまとめて、同じ役割の中で数値が飛び抜けているものがすぐ分かる。個人的には、エンタープライズかWebかというのは関係なく、すっと入ってきました。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">他のMackerelのユーザー企業はWebサービスやゲームのように、B2C系の会社が多かったんです。御社のように主に会社相手のビジネスの場合、Mackerelがどうフィットするのか気になっています。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-a">アイテック奥</b>
      <span class="speak">今までの監視ツールは、ツールごとにできることが違うし、何かやると費用が発生する。例えば私たちのお客様にサービスを提供するとき「URL監視一点につきいくらかかります」と費用が発生すると、サービスメニューを作るのが非常に面倒です。業務フェーズの監視となると従量課金という訳にもいかない。そうした細かいことを言うのが嫌なんです。
        <span class="speak-content">Mackerelは、エージェントを入れると、何でも値を取れる。お客様に提供するサービスメニューを作りやすい。我々のサービスにかなり合っています。</span>
      </span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">そういえば、御社は、というより森本さんが、かもしれませんが、結構新しいツールを使っていますよね。挑戦的な感じです。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">僕が、新しいツールを試しがちなんですね（笑）。ホスティングサーバを提供していますので、使っているソフトウェアもLinuxやApacheなどが多く、「オープンソースだからダメ」みたいな思いはありません。取り入れるべきものは取り入れる、新しいもの好きなところはあります。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-c">はてな松木</b>
      <span class="speak">SIerでありつつも、先進的な取り組みをしているなという印象があります。そういう風土が、Mackerelの採用に至ったポイントでしょうか。</span>
    </p>
    <p class="dialog">
      <b class="speaker member-b">アイテック森本</b>
      <span class="speak">Mackerelの設計思想がいい部分はありました。オープンソースは無償だけど運用コストはかかる。監視サーバーの機能やVMの料金や運用コストが個別に発生します。総合的には考えるとMackerelにお金を払う価値は十分にあると思っています。</span>
    </p>
    <b class="speaker member-a">アイテック奥</b>
      <span class="speak">VM数は増え続けるので、監視にあまり手をかけたくないし、かといってサボる訳にはいかない。エージェントを入れると自動的にやってくれるのはいいですね。</span>
    </p>
    <p class="dialog-interviewer">
      <b class="dash"></b>
      <span class="interview">ありがとうございました。</span>
    </p>

    <figure>
      <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20181212/20181212175855.jpg" alt="20181212175855">
    </figure>
  </div>
</div>
