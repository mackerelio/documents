---
Title: TCPサーバーの確認をおこなう
Date: 2015-11-23T18:02:07+09:00
URL: https://mackerel.io/ja/docs/entry/howto/check/tcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653586347146220004
---

Mackerelでは公式チェックプラグインを利用することで、TCPサーバーの監視をおこなえます。

このページでは、チェックプラグインの導入からシンプルな監視設定までをご案内します。詳細な設定については、別途利用するプラグインのヘルプをご確認ください。

[:contents]

## TCPサーバー監視の始め方

### 1. 公式チェックプラグイン集をインストールする

[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) を参照し、公式チェックプラグイン集を導入します。

### 2. チェック監視の設定を追加する

監視対象となるサーバの mackerel-agent.conf の末尾に、監視したいTCPサーバーに合わせたチェック監視の設定をおこないます。
追加する設定は、以下のようなフォーマットになっています。

```toml
[plugin.checks.<監視ルール名>]
command = ["実行したいコマンド", "引数", "引数"...]
```

追記が完了したら、mackerel-agent を再起動（Linux の場合は reload でも可）することで、監視が開始されます。
設定が正常に反映された場合、設定で指定した監視ルール名が対象ホストの Monitors に表示されます。

以下に、check-tcpの設定例を示します。使用できるオプションなどについては、[check-tcpのヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-tcp)をご確認ください。

#### サービスを指定して利用する

いくつかのサービスに関しては `--service` オプションを指定することで、複雑な指定をせずに標準的なチェックをおこなえます。例えばFTPの場合だと以下のようになります。

```toml
[plugin.checks.ftp]
command = ["check-tcp", "--service", "ftp", "-H", "localhost"]
```

これは以下と等価の設定になっています。

```toml
[plugin.checks.ftp]
command = ["check-tcp", "-H", "localhost", "--port", "21", "--expect-pattern", "^200", "--quit", "QUIT"]
```

`--service` オプションに指定可能なサービス名については、[対応サービス一覧](https://mackerel.io/ja/docs/entry/plugins/check-tcp#services)を参照してください。

#### HTTPサーバーの確認をおこなう

以下はHTTPのアプリケーションサーバーに対して、`GET / HTTP/1.0\r\n\r\n` というリクエストを送り、レスポンスに `OK Farm` が含まれているかどうかの確認をおこなう設定です。`--escape` オプションを指定することで、`--send` の引数に含まれる `"\r"` や `"\n"` などの文字が、改行文字として扱われるようになります。

また、接続にかかった秒数に対して閾値が設定でき、下記の場合だと、3秒以上かかった場合にWarning、10秒以上の場合にCriticalが発生します。

```toml
[plugin.checks.tcp_app]
command = ["check-tcp", "--hostname", "localhost", "--port", "5000", "--send", "GET / HTTP/1.0\r\n\r\n", "--escape", "--expect-pattern", "OK Farm", "--warning", "3", "--critical", "10"]
```

### 3. チェック監視の動作を変更する

必要に応じて、チェック監視の実行間隔や、何回連続で異常を検知したらアラート発報するかなどの設定が可能です。以下はデフォルトでは実行間隔が1分のチェック監視を10分間隔に変更し、さらに3回実行した結果がすべて異常だった場合にアラートを発報する例です。

```toml
[plugin.checks.ftp]
command = ["check-tcp", "--service", "ftp", "-H", "localhost"]
check_interval = 10m
max_check_attempts = 3
```

そのほか、チェック監視に設定可能な項目については、[チェック監視項目を追加する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/custom-checks#items) をご参照ください。
