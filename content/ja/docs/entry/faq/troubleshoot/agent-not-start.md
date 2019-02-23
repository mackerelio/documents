---
Title: FAQ・エージェントが起動しない
Date: 2017-11-20T15:05:48+09:00
URL: https://mackerel.io/ja/docs/entry/faq/troubleshoot/agent-not-start
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812319468022
---

以下の点について、改めて確認してください。全てを確認した上で問題が解消されない場合は、Mackerelにログインした状態でヘッダ部分に表示される「サポートチームへ連絡」からお問い合わせください。

* インストール対象のOSが公式サポートされているものかどうかを確認してください。
    * [対象環境 - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/overview#support-environments)
* パッケージが正しくインストールされているかを確認してください。
* エージェントをインストールしているサーバーにウイルス対策ソフトウェアがインストールされている場合、mackerel-agentの通信が阻害されていないことを確認してください。
* mackerel-agent が出力しているログの内容を確認し、エラーが出力されていないかどうかを確認してください。
* 設定ファイル `mackerel-agent.conf` が正しく記述できているかどうか、コマンド `mackerel-agent configtest` によりチェックしてください。
* Docker ImageやAMIなどによりOSイメージ化した際に`/var/lib/mackerel-agent/id`もコピーしている場合、このファイルを削除する必要があります。
    * mackerel-agentは初回起動時にサーバーが発行したIDを`/var/lib/mackerel-agent/id`に保存します。以後はこのファイルに記載されたIDによりホストの識別を行います。
    * そのため、`/var/lib/mackerel-agent/id`がコピーされると実際は複数のホストがMackerel上では同一のホストと認識され、メトリックの投稿や監視が正しく動作しなくなります。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
