---
Title: Windows Serverにmackerel-agentをインストールする
Date: 2015-06-19T18:12:46+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/msi
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450098156188
---

[KCPS版Mackerelをご利用の場合](#kcps)

## エージェントをインストールする

GUIを用いてインストールする方法か、コマンドラインからインストールする方法を選べます。

あらかじめ [エージェントのインストール時に発生する通信](https://mackerel.io/ja/docs/entry/howto/install-agent#install) を許可してください。

### 1. インストーラーをダウンロードする

[新規ホストの登録画面](https://mackerel.io/my/instruction-agent)でMicrosoft Windowsを選択し、ダウンロードボタンをクリックします。ダウンロードしたインストーラーを監視対象のサーバーにコピーします。

### 2. エージェントをインストールする

#### GUIでインストールする場合
ダウンロードしたmsiファイルをダブルクリックし、ダイアログに従ってインストールしてください。

途中でAPIキーの入力を求められるので、[新規ホストの登録画面](https://mackerel.io/my/instruction-agent)でMicrosoft Windowsを選択し、「A. GUIでインストール」に表示されているAPIキーを入力します。APIキーは[WebコンソールのAPIキータブ](https://mackerel.io/my?tab=apikeys)でも取得できます。

#### コマンドラインからインストールする場合

[新規ホストの登録画面](https://mackerel.io/my/instruction-agent)でMicrosoft Windowsを選択し、「B. コマンドラインからインストール」に表示されているコマンドを、管理者権限のコマンドプロンプトで実行します。

```
msiexec /qn /i path/to/mackerel-agent-x64-latest.msi APIKEY="<YOUR_API_KEY>"
```

### 3. ホストの登録確認

インストール完了後にエージェントは自動で起動します。エージェントが起動するとMackerelにホストとして登録されます。[ホスト一覧画面](https://mackerel.io/my/hosts)を確認してください。

ホストが登録されない場合は、[FAQ：ホストが登録されない](https://support.mackerel.io/hc/ja/articles/30952244573337)の内容を確認してください。

## エージェントを再起動する

サービス一覧からmackerel-agentを再起動します。

## エージェントのログを確認する

mackerel-agentのログはイベントビューアーの Windowsログ > Application で確認できます。イベントソースはmackerel-agentです。

## エージェントをバージョンアップする

エージェントをバージョンアップする場合は、新しいバージョンのインストーラーで上書きインストールします。

最新のインストーラーは、[新規ホストの登録画面](https://mackerel.io/my/instruction-agent)でMicrosoft Windowsを選択し、ダウンロードボタンをクリックすることで入手できます。

## エージェントをアンインストールする

コントロールパネルの「プログラムと機能」からmackerel-agentをアンインストールします。

一部のファイルはアンインストールによって削除されずに残ります。不要な場合は削除してください。

- [エージェントのアンインストール時に削除されないファイル](https://mackerel.io/ja/docs/entry/howto/install-agent#uninstall-files)

<h2 id="kcps">KCPS版Mackerelをご利用の場合</h2>

KCPS版Mackerelをご利用の場合は、KCPSのナレッジサイトをご参照ください。

- [エージェントをインストールする](https://doc.cloud-platform.kddi.ne.jp/guidebook/hands-on/kansi/kihon/agent-install/)
- [エージェントを再起動する](https://doc.cloud-platform.kddi.ne.jp/guidebook/hands-on/kansi/kihon/agent-restart/)
- [エージェントのログを確認する](#エージェントのログを確認する)（本ページに記載の方法で確認できます）
- [エージェントをバージョンアップする](https://doc.cloud-platform.kddi.ne.jp/guidebook/hands-on/kansi/kihon/mackerel-versionup/)
- [エージェントをアンインストールする](https://doc.cloud-platform.kddi.ne.jp/faq/functions_operations/partner-service-functionoperation/mackerel-partner-service-functionoperation/17607/)
