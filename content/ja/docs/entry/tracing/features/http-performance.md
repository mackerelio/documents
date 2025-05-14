---
Title: トレース - HTTPサーバーのパフォーマンスを調査する
Date: 2025-03-21T11:03:20+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/features/http-performance
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398338191359
---

APMサービス画面のHTTPサーバータブでは、アプリケーションのトレーススパンに含まれるHTTPリクエストのパフォーマンスに関する統計情報を一覧で確認できます。

<figure class="figure-image figure-image-fotolife" title="HTTPサーバーパフォーマンス画面">[f:id:mackerelio:20250317142308p:plain]<figcaption>HTTPサーバーパフォーマンス画面</figcaption></figure>

## リクエストの一覧を確認する

HTTPサーバーパフォーマンス画面では、受信したリクエストに関するトレーススパンの情報が一覧で表示されます。送信時のトレーススパン（クライアントスパン）に関しては集計対象外です。

各ルートの行には以下の情報が表示されます。ルート以外の項目名をクリックすることで、その項目を基準に昇順と降順を切り替えられます。

| 項目      | 内容                             |
|-----------|---------------------------------|
| ルート       | リクエスト先                          |
| 合計      | ルートごとのレスポンスタイムの合計時間          |
| 平均      | ルートごとのレスポンスタイムの平均時間          |
| P95（近似） | レスポンスタイムの95パーセンタイル値（近似値）     |
| エラー率     | ルートごとのエラー発生率                  |
| アクセス数    | ルートごとのアクセス数                     |
| トレース      | このクエリが含まれるトレースの一覧画面に遷移します |

- ルートには以下の属性の値が表示されます
  - URL部分
    - `http.route` / `url.path` / `http.target` / `http.url` のいずれか
      - 記述した順に優先されます。`http.route` の値が最優先です
      - いずれの属性も含まないトレーススパンは一覧に表示されません
  - メソッド部分
    - `http.request.method` / `http.method` のいずれか
      - `http.request.method` の値が優先されます
      - いずれの属性も含まないトレーススパンは一覧に表示されません
- P95（近似）はMackerel側で算出した近似値であり、正確な95パーセンタイル値ではありません
- エラー率の計算の仕組みは以下の通りです
  - ステータスコード500番台をエラーとして扱い、`エラー数 / リクエスト総数` で求められます
  - `http.response.status_code` / `http.status_code` のうちいずれかの属性の値がエラー率の計算に使用されます
    - `http.response.status_code` の値が優先されます
    - いずれの属性も含まないトレーススパンは一覧に表示されません
