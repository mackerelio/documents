---
Title: TCPサーバーの確認を行う
Date: 2015-11-23T18:02:07+09:00
URL: https://mackerel.io/ja/docs/entry/howto/check/tcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653586347146220004
---

公式チェックプラグイン集の `check-tcp` を使ってTCPサーバーの監視をおこなえます。公式チェックプラグイン集のインストールは、[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) をごらんください。

## HTTPサーバーの確認を行う

以下はHTTPのアプリケーションサーバーに対して、 `GET / HTTP/1.0\r\n\r\n` というリクエストを送り、レスポンスに "OK Farm" が含まれているかどうかの確認をおこなう設定です。 `--escape` オプションを指定することで、`--send` の引数に含まれる `"\r"` や `"\n"` などの文字が、改行文字として扱われるようになります。

また、接続にかかった秒数に対して閾値が設定でき、下記の場合だと、3秒以上かかった場合にWarning、10秒以上の場合にCriticalが発生します。

```config
[plugin.checks.tcp_app]
command = ["check-tcp", "--hostname", "localhost", "--port", "5000", "--send", "GET / HTTP/1.0\r\n\r\n", "--escape", "--expect-pattern", "OK Farm", "--warning", "3", "--critical", "10"]
```

## サービスを指定して利用する

もちろん、HTTP以外のサーバーのチェックも可能です。いくつかのサービスに関しては `--service` オプションを指定することで、複雑な指定すること無しに標準的なチェックをおこなえるようになっています。例えばFTPの場合だと以下のようになります。

```config
[plugin.checks.ftp]
command = ["check-tcp", "--service", "ftp", "-H", "localhost"]
```

これは以下と等価の設定になっています。

```config
[plugin.checks.ftp]
command = ["check-tcp", "-H", "localhost", "--port", "21", "--expect-pattern", "^200", "--quit", "QUIT"]
```

`--service` オプションに指定可能な設定は、FTP, POP, SPOP, IMAP, SIMAP, SMTP, SSMTPとなっています。

その他のオプションに関しては `check-tcp --help` や [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-tcp/README.md) をご確認下さい。

## ソースコードについて

`check-tcp`のソースコードは以下に公開されています。

<https://github.com/mackerelio/go-check-plugins/tree/master/check-tcp>
