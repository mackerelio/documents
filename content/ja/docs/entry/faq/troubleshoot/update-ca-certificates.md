---
Title: 'FAQ・エージェントが「x509: certificate signed by unknown authority」エラーを出力しています'
Date: 2020-05-08T11:29:33+09:00
URL: https://mackerel.io/ja/docs/entry/faq/troubleshoot/update-ca-certificates
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613563828584
---

mackerel-agent が以下のようなログを出力した場合、

```
Failed to post metrics value (will retry): Post https://mackerel.io/api/v0/tsdb: x509: certificate signed by unknown authority
```

mackerel-agent がインストールされているホストのCA証明書が古い可能性があります。
`ca-certificates` パッケージの更新や Windows Update の実施をご検討ください。（`will retry` という出力のとおり、通信が正常な状態に復帰した際にメトリックの投稿はリトライされます。）

また、ご利用のOSが[公式サポート対象](https://mackerel.io/ja/docs/entry/overview#support-environments)のものであることも合わせてご確認ください。


これは例えば、SSL/TLS通信を確立するために必要となる証明書の更新により発生する場合があります。この事象は Mackerel に限らず、そのホストから SSL/TLS 通信を行う全ての場合に起こり得るものとなりますので、ホストのCA証明書は定期的に更新することをおすすめいたします。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
