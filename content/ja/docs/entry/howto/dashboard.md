---
Title: カスタムダッシュボードを利用する
Date: 2018-10-30T18:58:18+09:00
URL: https://mackerel.io/ja/docs/entry/howto/dashboard
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132661894084
---

# カスタムダッシュボードを利用する
本機能では標準でご利用いただけるメインダッシュボードに追加する形で、ユーザー自身で独自にカスタマイズしたダッシュボードを作成できます。

## カスタムダッシュボードを作成する
画面左側メニューにあるDashboardをクリックすると、右側メニューがダッシュボードの管理画面へと変わります。

そこから「カスタムダッシュボードを追加」ボタンをクリックします。

[f:id:mackerelio:20181102170938p:plain]

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

## 注意事項
- 1つのオーガニゼーションにつき、作成できるカスタムダッシュボードの数には上限があります（Standardプランの場合、100個）。ご利用のオーガニゼーションで作成できるカスタムダッシュボードの数は[こちら](https://mackerel.io/my?tab=plan)からご確認ください。
- 有料プランからFreeプランへダウングレードされた場合、作成済のカスタムダッシュボードのうち上限の個数を超えた分は、削除されず無効化されます。
