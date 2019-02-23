---
Title: FAQ・インスタンスを再作成しても同じホストとして認識させたい
Date: 2017-11-17T17:38:24+09:00
URL: https://mackerel.io/ja/docs/entry/faq/spec/host-inheriting
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812318610518
---

## Amazon EC2 や Azure VM の場合
それまで監視対象としていたインスタンスとは異なるインスタンスを、Mackerel側では同じホストとして認識させる（情報を引き継ぐ）ことはできません。
既存のホストを[退役](https://mackerel.io/ja/docs/entry/howto/host-retirement)させ、新たなホストとして監視を開始してください。

## 上記以外の仮想ホスト・VMの場合
インスタンスの再作成をする際、再作成の前後で同じホストと認識させたい場合はホストID（Linux系OSのホストの場合は `/var/lib/mackerel-agent/id` に、Windows Server の場合はインストールフォルダ内の `id` ファイルに、保存されています。詳しくは[用語集のホストの項目](https://mackerel.io/ja/docs/entry/glossary#host)を参照ください）が記録されたファイルをインスタンスの再作成前に退避しておき、再作成した後にそのファイルを配置・エージェントを起動することで対応できます。

ただし、同じホストIDで複数の mackerel-agent が動作すると、メトリックの投稿や監視が正しく動作しなくなりますので、ご注意ください。（IDが同じホストが複数存在する場合、Mackerel ではそれぞれのホストからのメトリック送信を同一ホストからのメトリック送信とみなし、メトリック値の上書きをおこなってしまいます。）

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
