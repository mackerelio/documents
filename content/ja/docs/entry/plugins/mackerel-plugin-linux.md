---
Title: メトリックプラグイン - mackerel-plugin-linux
Date: 2023-04-10T18:21:37+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-linux
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889979931444
---

mackerel-plugin-linux は Linux の情報を毎分集計し、メトリックとして投稿するプラグインです。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

### Linux Users

| メトリック表示名 | メトリック名            | 差分  | 積み上げ表示 | 説明         |
| -------- | ----------------- | --- | ---- | ---------- |
| Users    | linux.users.users | -   | -      | ログイン中のユーザ数 |

who コマンドの結果を参照。


### Linux Interrupts

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Interrupts | linux.interrupts.interrupts | ○ | - | システムで発生した割り込み数 |

/proc/stat の intr の値を参照。


### Linux Context Switches

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Context Switches | linux.context_switches.context_switches | ○ | - | コンテキストスイッチの回数 |

/proc/stat の ctxt の値を参照。


### Linux Forks

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Forks | linux.forks.forks | ○ | - | フォーク（fork）の回数 |

/proc/stat の processes の値を参照。


### Disk Elapsed IO Time

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| <device\> IO Time | linux.disk.elapsed.iotime_<device\> | ○ | - | このブロックデバイスがアクティブになっていた合計時間（ミリ秒） |
| <device\> IO Time Weighted | linux.disk.elapsed.iotime_weighted_<device\> | ○ | - | 全リクエストの合計待機時間（ミリ秒） |

/sys/block/<device\>/stat の io_ticks および time_in_queue の値を参照。<device\> はデバイス名に置き換えられます。


### Disk Read/Write Time

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| <device\> Read | linux.disk.rwtime.tsreading_<device\> | ○ | - | 読み取りリクエストの合計待機時間（ミリ秒） |
| <device\> Write | linux.disk.rwtime.tswriting_<device\> | ○ | - | 書き込みリクエストの合計待機時間（ミリ秒） |

/sys/block/<device\>/stat の read ticks および write ticks の値を参照。<device\> はデバイス名に置き換えられます。

### Linux Network Connection States

| メトリック表示名     | メトリック名              | 差分  | 積み上げ表示 | 説明            |
| -------- | ----------------- | --- | ---- | ---------- |
| Established  | linux.ss.ESTAB      | -   | ○      | ESTAB の数      |
| Syn Sent     | linux.ss.SYN-SENT   | -   | ○      | SYN-SENT の数   |
| Syn Received | linux.ss.SYN-RECV   | -   | ○      | SYN-RECV の数   |
| Fin Wait 1   | linux.ss.FIN-WAIT-1 | -   | ○      | FIN-WAIT-1 の数 |
| Fin Wait 2   | linux.ss.FIN-WAIT-2 | -   | ○      | FIN-WAIT-2 の数 |
| Time Wait    | linux.ss.TIME-WAIT  | -   | ○      | TIME-WAIT の数  |
| Close        | linux.ss.UNCONN     | -   | ○      | UNCONN の数     |
| Close Wait   | linux.ss.CLOSE-WAIT | -   | ○      | CLOSE-WAIT の数 |
| Last Ack     | linux.ss.LAST-ACK   | -   | ○      | LAST-ACK の数   |
| Listen       | linux.ss.LISTEN     | -   | ○      | LISTEN の数     |
| Closing      | linux.ss.CLOSING    | -   | ○      | CLOSING の数    |
| Unknown      | linux.ss.UNKNOWN    | -   | ○      | UNKNOWN の数    |

ss コマンドの結果を State ごとにカウント。


### Linux Swap Usage

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| -------- | ----------------- | --- | ---- | ---------- |
| Swap In | linux.swap.pswpin | ○ | - | スワップインしたページ数 |
| Swap Out | linux.swap.pswpout | ○ | - | スワップアウトしたページ数 |

/proc/vmstat の pswpin および pswpout の値を参照。


<h2 id="options">指定可能なオプション</h2>

| オプション | 省略形 | 説明 | デフォルト値 |
| --- | --- | --- | --- | 
| --tempfile | -t | tempfile の保存先ファイルパスの指定 |  |
| --type | -p | 取得するメトリックの絞り込み<br>all, users, proc_stat, diskstats, netstat, swap のいずれかを指定 | all |
| --version | -v | プラグインのバージョンを表示 |  |
| --help | -h | ヘルプを表示 |  |

tempfile には集計結果が記録されます。デフォルトでは `/var/tmp/mackerel-agent/` 配下に `mackerel-plugin-linux-<ハッシュ文字列>` の形式で作成されます。

<h2 id="config">エージェントへの設定例</h2>

```toml
[plugin.metrics.linux]
command = ["mackerel-plugin-linux"]
```

<h2 id="repository">リポジトリ</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-linux]
