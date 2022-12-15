---
Title: メトリックプラグイン - mackerel-plugin-windows-server-sessions
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-windows-server-sessions
---

mackerel-plugin-windows-server-sessions は Windows の共有フォルダのセッション数をメトリックとして監視できます。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

### Windows Server Sessions

| メトリック表示名          | メトリック名                                  | 差分 | 積み上げ表示 | 説明                        |
| ------------------------- | --------------------------------------------- | :----: | :------------: | --------------------------- |
| count | custom.windows.server.sessions.#.count | -    | -            | 共有フォルダのセッション数 |

- メトリック名の `#` の箇所はコンピュータ名に置き換えられます
- `net session` コマンドで表示されるセッション数を投稿します（リモートデスクトップなどでログインしているユーザの数ではありません）


<h2 id="config">エージェントへの設定例</h2>

```
[plugin.metrics.td-table-count]
command = ["mackerel-plugin-windows-server-sessions"]
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### メトリックが投稿されない
このプラグインは WMI（Windows Management Instrumentation）の Win32_PerfFormattedData_PerfNet_Server クラスの情報を取得しています。メトリックが投稿されない場合は、原因切り分けのためコマンドプロンプトで以下のコマンドを実行してください。

`WMIC PATH Win32_PerfFormattedData_PerfNet_Server GET ServerSessions /FORMAT:CSV`

正常時は以下のような出力結果になります。

```
Node,ServerSessions
<コンピュータ名>,0
```

エラーが発生した場合は WMI 側の問題が疑われます。イベントビューアで WMI 関連のエラーなどが発生していないか確認してください。


<h2 id="repository">リポジトリ</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-windows-server-sessions]
