---
Title: チェックプラグイン - check-procs
Date: 2022-12-16T15:58:07+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/check-procs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889945587655
CustomPath: plugins/check-procs
---

check-procs は プロセスの数を監視するチェックプラグインです。指定した条件に一致するプロセス数をカウントし、その数が閾値よりも少ないか多い場合にアラートを発生させます。

[:contents]

<h2 id="options">指定可能なオプション</h2>

| オプション | 省略形 | 説明 | デフォルト値 |
| --- | --- | --- | --- |
| --warning-over | -w | 条件に一致するプロセス数が指定値よりも多い場合に Warning アラートを発生 |  |
| --critical-over | -c | 条件に一致するプロセス数が指定値よりも多い場合に Critical アラートを発生 |  |
| --warning-under | -W | 条件に一致するプロセス数が指定値よりも少ない場合に Warning アラートを発生 | 1 |
| --critical-under|-C| 条件に一致するプロセス数が指定値よりも少ない場合に Critical アラートを発生 | 1 |
| --match-self | -m | check-procs の実行プロセス自体をカウントの対象にする |  |
| --match-parent | -M | check-procs の親プロセスをカウントの対象にする |  |
| --pattern | -p | 対象にするプロセス名のパターンを正規表現で指定 |  |
| --exclude-pattern | -x | 除外したいプロセス名のパターンを正規表現で指定 |  |
| --ppid |  | 指定した親プロセス ID に一致するプロセスを対象にする |  |
| --file-pid | -f | 指定したプロセス ID に一致するプロセスを対象にする |  |
| --virtual-memory-size | -z | 仮想メモリの容量が指定値以下のプロセスを対象にする |  |
| --resident-set-size | -r | 物理メモリの容量が指定値以下のプロセスを対象にする |  |
| --proportional-set-size | -P | CPU 使用率が指定値以下のプロセスを対象にする |  |
| --thread-count | -T | スレッド数が指定値以下のプロセスを対象にする |  |
| --state | -s | 指定した状態のプロセスを対象にする |  |
| --user | -u | 指定したユーザによって実行されているプロセスを対象にする |  |
| --user-not | -U | 指定したユーザによって実行されていないプロセスを対象にする |  |
| --esec-over | -e | プロセスが起動してからの経過時間が指定値を超えるプロセスを対象にする |  |
| --esec-under | -E | プロセスが起動してからの経過時間が指定値に満たないプロセスを対象にする |  |
| --cpu-over | -i | プロセスが起動してから使用した CPU の時間が指定値を超えるプロセスを対象にする |  |
| --cpu-under | -I | プロセスが起動してから使用した CPU の時間が指定値に満たないプロセスを対象にする |  |
| --help | -h | ヘルプを表示 |  |

オプションを指定しない場合はすべてのプロセスがカウントの対象になります。

<h3 id="options-parameter">プロセス情報の取得元</h3>

オプションで指定可能なプロセス情報の取得元は下記の通りです。

- Linux
  - ps コマンドの結果
- Windows
  - WMI の Win32_PerfFormattedData_PerfProc_Process クラス

| オプション | Linux | Windows |
| --- | --- | --- |
| --pattern / --exclude-pattern | COMMAND | Name |
| --user | USER | 非対応 |
| --user-not | USER | 非対応 |
| --ppid | PPID | 非対応 |
| --file-pid | PID | IDProcess |
| --virtual-memory-size | VSZ | VirtualBytes |
| --resident-set-size | RSS | WorkingSet |
| --proportional-set-size | %CPU | PercentProcessorTime |
| --thread-count | NLWP | ThreadCount |
| --state | STATE | 非対応 |
| --esec-over | ELAPSED | ElapsedTime |
| --esec-under | ELAPSED | ElapsedTime |
| --cpu-over | TIME | 常に 0 |
| --cpu-under | TIME | 常に 0 |


<h2 id="config">エージェントへの設定例</h2>

監視対象のプロセスが 1 つも存在しない場合にアラートを発生させる設定は以下です。

```
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME"]
```

下記の例では監視対象のプロセスの数が 5 個を下回ると Warning、10 個を上回ると Critical になります。

```
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-under", "5", "--critical-over", "10"]
```

<h2 id="tips">Tips</h2>

### プロセスが 1 つ以上存在する場合にアラートを発生させる

プロセスが存在しない状態が正常というケースで、プロセスが 1 つ以上存在する場合にアラートを発生させたい場合、`--warning-under` と `--critical-under` それぞれに 0 を指定します。これらのオプションはデフォルト値が 1 になっているため両方の指定が必要です。

下記の例では監視対象のプロセスの数が 0 個の時に OK、1 個以上で Warning、2 個以上で Critical になります。

```
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-under", "0", "--critical-under", "0", "--warning-over", "0", "--critical-over", "1"]
```

### Warning のアラートのみ発生させる

監視対象のプロセスの数が閾値より少ない場合に Critical アラートを発生させたくない場合は、`--critical-under` に 0 を指定します。

下記の例では監視対象のプロセスが 1 個も存在しない場合に Warning アラートのみ発生します。

```
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-under", "1", "--critical-under", "0"]
```

監視対象プロセスの数が閾値より多い場合に Critical アラートを発生させたくない場合は、`--critical-over` に大きな数値を指定します。

下記の例では監視対象のプロセスが 1 個以上存在する場合に Warning アラートのみ発生します。`--critical-over` の値は環境に合わせて調整してください。

```
[plugin.checks.check-procs-sample]
command = ["check-procs", "--pattern", "PROCESS_NAME", "--warning-over", "0", "--critical-over", "9999"]
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### Windows 環境で "Procs UNKNOWN: 例外が発生しました。 (無効なクラスです)" が発生する

このエラーは WMI の Win32_PerfFormattedData_PerfProc_Process クラスの情報が取得できない場合に発生します。原因切り分けのためコマンドプロンプトで以下のコマンドを実行してください。

`WMIC PATH Win32_PerfFormattedData_PerfProc_Process GET /FORMAT:LIST`

正常時は [プロセス情報の取得元](#options-parameter) に記載した表の Name や IDProcess などの各項目がプロセスごとに標準出力されます。

エラーが発生した場合は WMI の問題が疑われます。イベントビューアで WMI 関連のエラーなどが発生していないか確認してください。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-procs
