---
Title: カスタムダッシュボードを利用する
Date: 2018-10-30T18:58:18+09:00
URL: https://mackerel.io/ja/docs/entry/howto/dashboard
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132661894084
---

# カスタムダッシュボードを利用する
本機能では標準でご利用いただけるメインダッシュボードに追加する形で、ユーザー自身で独自にカスタマイズしたダッシュボードを作成できます。

## カスタムダッシュボードを作成する
画面左側メニューにあるダッシュボードをクリックすると、右側メニューがダッシュボードの管理画面へと変わります。

そこから「カスタムダッシュボード作成」ボタンをクリックします。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240909/20240909103959.png" alt="f:id:mackerelio:20240909103959.:plain" title="f:id:mackerelio:20240909103959:plain" class="hatena-fotolife" itemprop="image"></span></p>

### テンプレートから作成する

「カスタムダッシュボードおまかせ生成」ボタンをクリックします。

テンプレートからダッシュボードを作成するダイアログが表示されます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240909/20240909143329.png" alt="f:id:mackerelio:20240909143329:plain" title="f:id:mackerelio:20240909143329:plain" class="hatena-fotolife" itemprop="image"></span></p>

タイトルにはこれから作成するカスタムダッシュボードの名称を入力してください。

作成したいダッシュボードに適したテンプレートを選択して、「次へ」ボタンをクリックしてください。
利用可能なテンプレートとそれぞれの詳細は下記の [カスタムダッシュボードおまかせ生成で利用可能なテンプレート](#templates) をご参照ください。

テンプレートごとに必要な項目を入力または選択して、「作成」ボタンをクリックしてください。
設定した内容と投稿されているメトリックをもとに、生成されたダッシュボードのページに遷移します。

生成されたダッシュボードを編集したい場合は、次項の [手動で作成する](#manual) を参照してください。

<h3 id="manual">手動で作成する</h3>

「任意で作成する」ボタンをクリックします。

新しいダッシュボードを作成する画面に遷移します。

タイトルにはこれから作成するカスタムダッシュボードの名称を入力して下さい。

画面下部の枠内にアイコンをドラッグ&amp;ドロップすることで、ウィジェットを作成できます。ウィジェットについては、下記「カスタムダッシュボードに配置可能なウィジェット」をご覧ください。

[f:id:mackerelio:20181102173027p:plain]

ウィジェットを作成後、ウィジェットの内容を編集したり、ウィジェットを削除する場合は、各ウィジェットの右上に表示されるボタンを押します。また、ウィジェットをドラッグ&amp;ドロップすることで、ウィジェットを並び替えたり、大きさを変更できます。Alt（option）キーを押しながらドラッグ&amp;ドロップでウィジェットを複製できます。

[f:id:mackerelio:20181102173050p:plain]

直前の操作を取り消したい場合は「元に戻す」ボタンを押します。

[f:id:mackerelio:20181101151553p:plain]

編集が終わりましたら、作成ボタンで作業を完了させます。

## カスタムダッシュボードに配置可能なウィジェット
カスタムダッシュボードには、以下のウィジェットを配置できます。機能の詳細については各ページでご確認ください。

<dl>
    <dt><a href="https://mackerel.io/ja/docs/entry/howto/dashboard/graph">グラフウィジェット</a></dt>
    <dd>各種グラフを表示できます。</dd>
    <dt><a href="https://mackerel.io/ja/docs/entry/howto/dashboard/value">数値ウィジェット</a></dt>
    <dd>各種メトリックの最新の値を数字で表示できます。</dd>
    <dt><a href="https://mackerel.io/ja/docs/entry/howto/dashboard/markdown">Markdownウィジェット</a></dt>
    <dd>Markdown形式で自由に内容を記述できます。</dd>
    <dt><a href="https://mackerel.io/ja/docs/entry/howto/dashboard/alert-status">アラートステータスウィジェット</a></dt>
    <dd>あるロールに所属している全てのホストのアラートステータスを表示できます。</dd>
</dl>

<h2 id="templates">カスタムダッシュボードおまかせ生成で利用可能なテンプレート</h2>

### 三層構造テンプレート

Web アプリケーションに典型的な、Web サーバー・App サーバー・DB サーバーによる三層構造システムのダッシュボードです。

Web・App・DB のロールを指定します。
各ロールにて、テンプレートが対応しているメトリックが投稿されている場合、そのメトリックを表示するウィジットが自動で作成されます。

各ロールが現在対応しているプラグインとクラウドインテグレーションは以下の通りです。

- Web ロール
  - AWSインテグレーション - ALB
  - AWSインテグレーション - API Gateway
  - AWSインテグレーション - CloudFront
  - AWSインテグレーション - EC2
  - mackerel-plugin-accesslog
  - mackerel-plugin-apache2
  - mackerel-plugin-nginx
- App ロール
  - AWSインテグレーション - EC2
  - mackerel-plugin-gostats
  - mackerel-plugin-jvm
  - mackerel-plugin-jmx-jolokia
- DB ロール
  - AWSインテグレーション - RDS
  - AWSインテグレーション - EC2
  - mackerel-plugin-postgres
  - mackerel-plugin-mysql

また、各ロールにてmackerel-agentやmackerel-container-agentによってシステムメトリックが投稿されている場合、システムメトリックを表示するウィジットが作成されます。

## 注意事項
- 1つのオーガニゼーションにつき、作成できるカスタムダッシュボードの数には上限があります（Standardプランの場合、100個）。ご利用のオーガニゼーションで作成できるカスタムダッシュボードの数は[こちら](https://mackerel.io/my?tab=plan)からご確認ください。
- 有料プランからFreeプランへダウングレードされた場合、作成済のカスタムダッシュボードのうち上限の個数を超えた分は、削除されず無効化されます。
