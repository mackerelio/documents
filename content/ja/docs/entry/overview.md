---
Title: 概要
Date: 2014-04-30T15:57:20+09:00
URL: https://mackerel.io/ja/docs/entry/overview
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722985455
---

Mackerelはロール（役割）の概念を用いて、ホストの運用・管理を簡単かつ便利に行えるようにするサービスです。あなたが管理するホストでmackerel-agentというプログラムを動かすことで、ウェブ上やAPI経由で複数のホストを管理し、また、さまざまな側面からホストやアプリケーションの状況を可視化できます。

<!-- [Features](https://mackerel.io/ja/features)もあわせてご覧ください。-->

## Mackerel（ウェブ）

ウェブ上（https://mackerel.io/）では、以下のようなことが行えます:

- 登録済ホストの一覧
- ホスト情報の編集
- サービスおよびロールによるホストの分類
- ロールごとのホストのリソース情報の可視化
- ホスト個別のリソース情報の可視化

## mackerel-agent

mackerel-agentはお使いのホストにインストールするプログラムです。mackerel-agentがMackerelにホストの情報や定期的に計測されるメトリックを送信し、ウェブ上でホストの管理やリソース情報の可視化などが行えるようになります。


<h3 id="support-environments">対応環境</h3>

Mackerelおよびmackerel-agentは、以下の環境で動作確認を行っています。

- Mackerel（ウェブ）
  - [Google Chrome](http://www.google.co.jp/intl/ja/chrome/browser/) 最新版
  - [Mozilla Firefox](http://www.mozilla.org/firefox/) 最新版
- mackerel-agent
    - Linux
        - CentOS 6/7、Ubuntu 14.04LTS/16.04LTS/18.04LTS、 Debian 8/9、Amazon Linux
        - その他、一般的なLinux環境(カーネルバージョン 2.6.23以降)でも動作すると思われますが、現在のところサポート対象外です。
    - Windows
        - Windows Server 2008 R2以降 の 32ビット/64ビット環境
    - またAmazon Web ServicesやMicrosoft Azure、Google Compute EngineなどのパブリックIaaSクラウド全般を動作対象としています。（ネットワーク環境が特殊なクラウドなどでは全ての機能が正常に動作しない可能性はあります。）

<h3 id="tsdb-spec">メトリックの保持について</h3>

Mackerelはメトリックを時系列データベース上に保存しています。
メトリックデータは1分間隔で保存され、以下の表の期間保持します。

| 間隔  | 保持期間  |
|-------|-----------|
| 1分   | 460日     |
| 1日   | 460日以降 |

これはデータの保持期間であり、グラフの表示期間とは異なります。グラフの表示期間は、ご利用のプランに基づきます。
