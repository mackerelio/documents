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
  - [Google Chrome](https://www.google.com/chrome/) (デスクトップ版) 最新版
  - [Mozilla Firefox](https://www.mozilla.org/firefox/) (デスクトップ版) 最新版
  - [Microsoft Edge](https://www.microsoft.com/edge) (デスクトップ版) 最新版
- mackerel-agent
    - Linux
        - Red Hat Enterprise Linux 8/9、MIRACLE LINUX 8/9、AlmaLinux 8/9、Rocky Linux 8/9、Oracle Linux 8/9
        - CentOS Stream 9
        - Ubuntu 20.04LTS/22.04LTS/24.04LTS、Debian 11/12
        - Amazon Linux 2、Amazon Linux 2023
        - x64 アーキテクチャおよび AWS Graviton シリーズを含む Arm v8 アーキテクチャをサポートしています。
        - その他、一般的なLinux環境(カーネルバージョン 3.2以降)や、上記以外のRed Hat Enterprise Linux派生ディストリビューションでも動作すると思われますが、現在のところサポート対象外です。
    - Windows
        - Windows Server 2016, 2019, 2022 (LTSC), 2025 (LTSC) の x64 ベースシステム
        - 2021年9月にリリースされた「信頼された署名プログラムに対するWindowsサポート」のWindows Update（KB5022661）が適用されている必要があります。
        - 署名されたモジュールを正しく検証するために、"Microsoft Identity Verification Root Certificate Authority 2020" 証明機関 (CA) がインストールされている必要があります。
        - 詳細については [KB5022661 - 信頼された署名 (旧称 Azure Code Signing) プログラムに対する Windows サポート](https://support.microsoft.com/ja-jp/topic/kb5022661-%E4%BF%A1%E9%A0%BC%E3%81%95%E3%82%8C%E3%81%9F%E7%BD%B2%E5%90%8D-%E6%97%A7%E7%A7%B0-azure-code-signing-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B-windows-%E3%82%B5%E3%83%9D%E3%83%BC%E3%83%88-4b505a31-fa1e-4ea6-85dd-6630229e8ef4) を参照してください。
    - またAmazon Web ServicesやMicrosoft Azure、Google Compute EngineなどのパブリックIaaSクラウド全般を動作対象としています。（ネットワーク環境が特殊なクラウドなどでは全ての機能が正常に動作しない可能性はあります。）

<h3 id="tsdb-spec">メトリックの保持について</h3>

Mackerelはメトリックを時系列データベース上に保存しています。
メトリックデータは1分間隔で保存され、以下の表の期間保持します。

| 間隔  | 保持期間  |
|-------|-----------|
| 1分   | 460日     |
| 1日   | 460日以降 |

これはデータの保持期間であり、グラフの表示期間とは異なります。グラフの表示期間は、ご利用のプランに基づきます。
