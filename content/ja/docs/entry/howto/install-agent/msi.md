---
Title: Windowsにmackerel-agentをインストールする
Date: 2015-06-19T18:12:46+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/msi
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450098156188
---

[KCPS版Mackerelをご利用の場合はこちら](#kcps)

## インストーラーをダウンロードする

<https://mackerel.io/file/agent/msi/mackerel-agent-latest.msi> よりインストーラーをダウンロードし、監視対象のホストにコピーします。

## Windowsホストにエージェントをインストールする
GUIを用いてインストールする方法か、コマンドラインからインストールする方法かを選べます。

### GUIでインストールする
ダウンロードしたmsiファイルのアイコンをダブルクリックし、ダイアログに従ってインストールしてください。

途中で API KEY の入力を求められますが、これは[Mackerel管理画面のOrganizationのページにある、APIキータブ](https://mackerel.io/my?tab=apikeys)よりご確認いただけます。

### 管理者権限のコマンドプロンプトよりインストールする

監視対象のホスト上で、管理者権限のユーザーのコマンドプロンプトから`msiexec`コマンドを用いてインストールします。

    msiexec /qn /i path/to/mackerel-agent-latest.msi APIKEY="<YOUR API KEY>"

入力していただく`<YOUR API KEY>`は[Mackerel管理画面のOrganizationのページにある、APIキータブ](https://mackerel.io/my?tab=apikeys)よりご確認いただけます。
mackerel-agentのインストール完了後、特にメッセージなどは表示されませんが、正常にインストールが完了した場合は自動的にmackerel-agentが起動します。
mackerel-agentが起動しているか否かの確認はWindowsサービス管理画面より確認ができます。
名前はmackerel-agentです。状態が「開始」になっていれば起動しています。

## mackerel-agentのログを確認する

mackerel-agentのログは、Windowsイベントログのアプリケーションログに出力されています。動作を確認したい場合はそちらをご確認下さい。

## mackerel-agentをアップデートする

mackerel-agentのアップデートは、最新のインストーラーをダウンロードし、インストールしていただくことで最新版へアップデートできます。

アップデートはmackerel-agent側で新しい機能をがリリースされてそれを利用する場合や安定性の改善が行われた等のタイミングで実施してください。

mackerel-agentのアップデートをしなくても基本機能（メトリックの投稿・監視など）は動作いたします。

## mackerel-agentのアンインストール

mackerel-agentをアンインストールするには、コントロールパネルの「プログラムと機能」から削除できます。
アンインストールの後、デフォルトではidファイル等が残っているため、PCを再起動していただき`C:\Program Files (x86)\Mackerel`をフォルダごと削除して下さい。


<h1 id="kcps">KCPS版mackerel-agentをWindowsにインストールする</h1>

<http://repo-kcps.mackerel.io/file/msi/mackerel-agent-latest-kcps.msi> よりインストーラーをダウンロードし、監視対象のホストにコピーします。

KCPS上のホストから、下記のURLを用いて直接ダウンロードすることもできます。

    http://198.18.0.16/file/msi/mackerel-agent-latest-kcps.msi

## Windowsホストにエージェントをインストールする
GUIを用いてインストールする方法か、コマンドラインからインストールする方法かを選べます。

### GUIでインストールする
ダウンロードしたmsiファイルのアイコンをダブルクリックし、ダイアログに従ってインストールしてください。

## 管理者権限のコマンドプロンプトよりインストールする

監視対象のホスト上で、管理者権限のユーザーのコマンドプロンプトから`msiexec`コマンドを用いてインストールします。

    msiexec /qn /i path/to/mackerel-agent-latest-kcps.msi APIKEY="<YOUR API KEY>"

mackerel-agentのインストール完了後、特にメッセージなどは表示されませんが、正常にインストールが完了した場合は自動的にmackerel-agentが起動します。
mackerel-agentが起動しているか否かの確認はWindowsサービス管理画面より確認ができます。
名前はmackerel-agentです。状態が「開始」になっていれば起動しています。

## mackerel-agentのログを確認する

mackerel-agentのログは、Windowsイベントログのアプリケーションログに出力されています。動作を確認したい場合はそちらをご確認下さい。
