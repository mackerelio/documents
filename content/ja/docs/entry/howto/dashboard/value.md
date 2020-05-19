---
Title: 数値ウィジェットを利用する
Date: 2018-10-31T15:18:47+09:00
URL: https://mackerel.io/ja/docs/entry/howto/dashboard/value
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132662339692
---

# 数値ウィジェットを利用する
数値ウィジェットを利用すると、任意のメトリックの最新の値を数字としてカスタムダッシュボードに表示できます。

[f:id:mackerelio:20200518114544p:plain]

## 数値ウィジェットを作成する
数値ウィジェットのアイコンをドラッグ&amp;ドロップして、ウィジェットを作成する位置を決定します。

[f:id:mackerelio:20181102173316p:plain]

続いてウィジェットに表示するメトリックを選択します。選択できるメトリックは

- ホストメトリック
- サービスメトリック
- 式[^1]

のいずれかです。

[f:id:mackerelio:20200518114558p:plain]

選択が完了したら作成ボタンでウィジェットを作成します。

[^1]: 式によるメトリックは[実験的機能](https://mackerel.io/ja/docs/entry/advanced/experimental-features)となっています。
