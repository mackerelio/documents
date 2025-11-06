---
Title: プロセス監視をおこなう
Date: 2015-11-05T12:11:35+09:00
URL: https://mackerel.io/ja/docs/entry/howto/check/process
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415126986617
---

公式チェックプラグイン集の `check-procs` を使ってプロセス監視をおこなえます。公式チェックプラグイン集のインストールは、[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) をごらんください。

## 例. cronのプロセス監視をおこなう

cronの監視をおこなうには以下のように mackerel-agent.conf に記述し、mackerel-agentを再起動します。

```toml
[plugin.checks.check_cron]
command = ["check-procs", "--pattern", "crond"]
```

`--pattern` オプションには対象のプロセスにマッチさせる正規表現を指定します。この状態でcrondが動作を停止すると、アラートが発生し、プロセス復旧後に自動で閉じられます。以下はその様子です。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151105/20151105163711.png)

Windows Server の場合でも同様に記述することができますが、`--pattern` オプションに指定する監視対象プロセスについては、以下の点を注意してください。

- コマンド `tasklist` で取得できるプロセス名を指定してください。
    - `tasklist` コマンドの出力が `foobar.exe` のように拡張子を含んでいる場合は、以下のように拡張子を取り除いて指定してください。

```toml
[plugin.checks.check_foobar]
command = ["check-procs","--pattern","foobar"]
```

## プロセスの個数を監視したい

`check-procs` には以下の様なオプションがあり、プロセス数に閾値を設定可能になっています。ワーカーのプロセス数を監視させたい場合などに便利です。

- `-w`, `--warn-over`
  - 設定値を上回ったらwarning
- `-c`, `--critical-over`
  - 設定値を上回ったらcritical
- `-W`, `--warn-under`
  - 設定値を下回ったらwarning
- `-C`, `--critical-under`
  - 設定値を下回ったらcritical

例えば、Nginxのワーカー数を含めた監視をしたい場合は以下のように指定します。

    [plugin.checks.check_nginx_worker]
    command = ["check-procs", "-p", "nginx", "-W", "8", "-w", "10", "-C", "1", "-c", "30", "--user", "nginx"]

この場合、Nginxのワーカー数が8未満もしくは10より大きい場合にWarning、1未満もしくは30より大きい場合にCriticalとなります。`--user` オプションで実行ユーザーを指定し、より正しくワーカー数が取得できるように設定しています。

その他、`check-procs` にはプロセスの実行時間やプロセスの状態に対して監視をおこなうようなオプションもそろっています。詳しくは `check-procs --help` や [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-procs/README.md) をご確認下さい。

## ソースコードについて

`check-procs`のソースコードは以下に公開されています。

<https://github.com/mackerelio/go-check-plugins/tree/master/check-procs>
