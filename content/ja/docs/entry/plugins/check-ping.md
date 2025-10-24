---
Title: チェックプラグイン - check-ping
Date: 2025-01-15T18:14:24+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-ping
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398320323645
---

check-ping は ping 応答を監視するプラグインです。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション       | 省略形 | 説明                                                         | デフォルト値 |
|-------------|--------|--------------------------------------------------------------|---------|
| --host      | -H     | 監視対象のホスト名またはIPアドレス                                      |         |
| --count     | -n     | ping パケットを送信する回数。1回でもしきい値以内に応答があれば監視ステータスは OK になります             | 1       |
| --wait-time | -w     | ping パケットを送信してから応答が返ってくるまでの合計時間（RTT）のしきい値。単位：ミリ秒（ms） | 1000    |
| --status-as |        | 監視ステータスを上書きします。たとえば `CRITICAL=WARNING` と記述した場合、監視ステータスが CRITICAL のときは WARNING になります。カンマ区切りで複数指定が可能です |         |
| --help      | -h     | ヘルプを表示                                       |         |

- `--status-as` は mackerel-check-plugins v0.50.1 以降で利用可能です。

<h2 id="config">エージェントへの設定例</h2>

以下の設定では、192.168.1.1 に対して 5 回パケットを送信し、1回も 100ms 以内に応答がなければ Critical アラートが発生します。

```toml
[plugin.checks.check-ping-sample]
command = ["check-ping", "-H", "192.168.1.1", "-n", "5", "-w", "100"]
```

設定をテストする場合は、ターミナルなどで以下のようにプラグインを直接実行します。

```bash
check-ping -H 192.168.1.1 -n 5 -w 100
```

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-ping
