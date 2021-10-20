---
Title: ログ監視をおこなう
Date: 2015-11-05T12:11:14+09:00
URL: https://mackerel.io/ja/docs/entry/howto/check/log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415126986600
---

公式チェックプラグイン集の `check-log` を使ってログ監視をおこなえます。公式チェックプラグイン集のインストールは、[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) をごらんください。

## `check-log` の使い方

`check-log`を使ってログ監視をおこなうためには例えば以下のように mackerel-agent.conf に記述し、mackerel-agentを再起動します。

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL"]
```

Windows Server 環境の場合は、以下のように記述します（ログファイル `C:\log\access.log` を対象にログ監視する場合。対象ログファイルにBOM（バイトオーダーマーク）が付与されていると監視をおこなえませんのでご注意ください）。

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "C:\\log\\access.log", "--pattern", "FATAL"]
```

`--file` オプションに監視対象のファイルを、`--pattern` オプションに、エラー文言を検出したいパターンを正規表現で指定します。この場合、ログファイルに "FATAL" という文字列が出現した場合にアラートが発生します。

初回のチェックは、設定追加後に出力された差分に対してのみおこなわれます。初回チェックのタイミングで既に存在するファイルの内容全てに対してチェックをおこないたい場合は、`--check-first` オプションを指定してください。

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL", "--check-first"]
```

`--pattern` オプションは複数指定することができ、その場合 AND 条件（指定された全てのパターンを満たすログ出力行のみ検出）として扱われます。以下の例は、 "PRODUCTION" と "FATAL" という2つの文字列が含まれるログ出力行が出現した場合にアラートを発生させる場合の設定例です。

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "PRODUCTION", "--pattern", "FATAL"]
```

`--pattern` には、日本語（マルチバイト文字）を指定することもできますが、その場合には conf ファイルの文字コード（エンコーディング）が `UTF-8` である必要があります。

`--file` オプションには、glob形式でチェック対象を指定することもできます。

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/*.log", "--pattern", "FATAL"]
```


また、`--file-pattern` オプションにより監視対象としたいファイル名の条件を正規表現で指定することも可能です。

```config
[plugin.checks.access_log]
command = ["check-log", "--file-pattern", "/var/log/access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "FATAL"]
```

Windows Server環境では、ディレクトリの区切り文字 `\` と正規表現が競合するため、 `--search-in-directory` オプション、 `--file-pattern` オプションの両方を使用して、ログファイルが存在するディレクトリとファイル名の条件を個別に指定することをおすすめします。

```config
[plugin.checks.access_log]
command = ["check-log", "--search-in-directory", "C:\\log\\", "--file-pattern", "access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "FATAL"]
```

mackerel-agentは定期的にcheck-logを実行しますが、前回実行時までにチェックした行はスキップされます。実行間隔は `check_interval` （[参照](https://mackerel.io/ja/docs/entry/custom-checks)）で指定された間隔(デフォルト1分)です。

ログローテーションが発生した場合、ログは冒頭から読み直されます。正確には、前回チェック時よりもファイルサイズが小さくなっている場合にログローテーションが発生したとみなし、冒頭から読み直す挙動となっています。

## 検出されたログ出力行の内容をMackerel上で確認したい

コマンドに `--return` オプションを付加することで、検出されたログ出力行の内容が標準出力に出すことができます。その出力はMackerelに送信され、ホスト詳細画面やアラート画面に可視化されるようになりますので、このオプションをご利用の際には秘匿情報などが意図せず送信されないようにご注意下さい。

また、送信内容のサイズが大きい場合、表示が切り詰められることがあります。仕様の詳細については[チェックプラグイン仕様](https://mackerel.io/ja/docs/entry/custom-checks#plugin)をご確認ください。

## 発生頻度に対する閾値や除外パターンを指定する

エラー文言が1回だけ出る分には問題ないが、頻発した場合にアラートを上げたいケースに対しては以下のオプションが有用です。

- `-w`, `--warning-over`
  - 設定値より多くエラー行が検出された場合にwarning
- `-c`, `--critical-over`
  - 設定値より多くエラー行が検出された場合にcritical

また `--exclude` オプションを指定することで、除外パターンを指定することが可能です。

例えば以下の設定ではNginxのアクセスログを監視して、4xxや5xxのエラーの発生をチェックしています。

`--exclude` を指定することで"robots.txt"へのアクセスは除外するようにしています。また、 `--warning-over`, `--critical-over` を指定することで、1分間に3回より多く出現したらWarning、10回より多く出現したらCriticalになるように設定しています。

```config
[plugin.checks.access_status]
command = ["check-log", "--file", "/var/log/nginx/access.log", "--pattern", "HTTP/1\.[01]\" [45][0-9][0-9] ", "--exclude", "GET .*?robots\.txt HTTP/1\.[01]", "--warning-over", "3", "--critical-over", "10", "--return"]
```

その他のオプションに関しては `check-log --help` や [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-log/README.md) をご確認下さい。

## ソースコードについて

`check-log`のソースコードは以下に公開されています。

<https://github.com/mackerelio/go-check-plugins/tree/master/check-log>
