---
Title: ディスク監視をおこなう
Date: 2017-08-29T16:13:08+09:00
URL: https://mackerel.io/ja/docs/entry/howto/check/disk
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812293108124
---

公式チェックプラグイン集の `check-disk` を使ってディスク監視をおこなえます。公式チェックプラグイン集のインストールは、[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) をごらんください。

## `check-disk` の使い方

`check-disk`を使ってディスク監視をおこなうためには例えば以下のように mackerel-agent.conf に記述し、mackerel-agentを再起動します。

以下の例では、`/tmp` のディスク容量が10%以下の場合にWarning、5%以下の場合にCriticalが発生します。

```toml
[plugin.checks.disk]
command = ["check-disk", "--path", "/tmp", "--warning", "10%", "--critical", "5%"]
```

Windows Server 環境の場合は、以下のように記述します（Cドライブのディスク容量が10%以下の場合にWarning、5%以下の場合にCritical を発生させる場合）。

```toml
[plugin.checks.disk]
command = ["check-disk", "--path", "C:", "--warning", "10%", "--critical", "5%"]
```

`--warning` 、`--critical` オプションは、 "%" でなく、ディスク容量を直接指定することも可能です。
また、`--path` オプションは複数指定することができ、その場合 OR 条件として扱われます。

以下の例では、 "/tmp" と "/var" という2つのパーティションをチェックし、いずれかのパーティションで容量が10GBを下回った場合にWarning、5GBを下回った場合にCriticalが発生します。

```toml
[plugin.checks.disk]
command = ["check-disk", "--path", "/tmp", "--path", "/var", "--warning", "10", "--critical", "5", "--units", "GB"]
```

## 閾値を指定する

`check-disk` は、以下の様なオプションで監視対象のディスクに対しアラートを発生させる閾値を設定できます。

- `-w`, `--warning`
  - 設定値より空いているディスク容量、ディスク割合が少なかった場合にWarning
- `-c`, `--critical`
  - 設定値より空いているディスク容量、ディスク割合が少なかった場合にCritical
- `-W`, `--iwarning`
  - 設定値より空いているinode割合が少なかった場合にWarning（Linux系OSでのみ利用可能）
- `-K`, `--icritical`
  - 設定値より空いているinode割合が少なかった場合にCritical（Linux系OSでのみ利用可能）

## 監視するパーティションを指定する

`check-disk` はデフォルトでは全てのデバイスを監視しますが、ファイルシステムの種類やデバイスのマウントポイントを指定して監視することもできます。

- `-p`, `--path`
  - 指定したデバイスを監視する（複数指定可）
- `-x`, `--exclude-device`
  - 指定したデバイスを監視対象から除外する
- `-X`, `--exclude-type`
  - 指定したファイルシステムタイプのデバイスを監視対象から除外する（複数指定可）
- `-N`, `--include-type`
  - 指定したファイルシステムタイプのデバイスのみ監視する（複数指定可）

以下の例では、ファイルシステムタイプが"none"で"/tmp"以外のデバイスを監視しています。

```toml
[plugin.checks.disk]
command = ["check-disk", "--include-type", "none", "--exclude-device", "/tmp", "--warning", "10%", "--critical", "5%"]
```

その他のオプションに関しては `check-disk --help` や [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-disk/README.md) をご確認下さい。

## ソースコードについて

`check-disk`のソースコードは以下に公開されています。

<https://github.com/mackerelio/go-check-plugins/tree/master/check-disk>
