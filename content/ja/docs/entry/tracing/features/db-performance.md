---
Title: トレース - データベースのパフォーマンスを調査する
Date: 2025-03-19T16:34:24+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/features/db-performance
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398337749793
---

APMサービス画面のデータベースタブでは、アプリケーションのトレーススパンに含まれるデータベースクエリを一覧で確認できます。

この画面を活用することで、パフォーマンスを低下させる遅いクエリや、頻繁に呼び出されていて改善が必要なクエリを簡単に特定できます。

<figure class="figure-image figure-image-fotolife" title="データベースタブ">[f:id:mackerelio:20250724141734p:plain]<figcaption>データベースタブ</figcaption></figure>


## クエリの一覧を確認する

データベースパフォーマンス画面では、トレーススパンに含まれる `db.statement` 属性の値または `db.query.text` 属性の値として格納されているクエリの情報が一覧で表示されます（`db.query.text` 属性の値が優先されます）。どちらの属性もトレーススパンに含まれていない場合は表示されません。

各クエリの行には以下の情報が表示されます。クエリ以外の項目名をクリックすることで、その項目を基準に昇順と降順を切り替えられます。

| 項目      | 内容                             |
|-----------|--------------------------------|
| クエリ       | 実行されたクエリの内容                  |
| 合計      | クエリの合計実行時間                 |
| 平均      | クエリの平均実行時間                 |
| P95（近似） | クエリの実行時間の95パーセンタイル値（近似値） |
| 実行回数  | クエリの合計実行回数                 |
| トレース      | このクエリが含まれるトレースの一覧画面に遷移します |
