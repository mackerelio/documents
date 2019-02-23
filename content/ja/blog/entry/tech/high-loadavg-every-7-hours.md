---
Title: Linuxのloadavgが約7時間ごとに上昇する現象の原因
Category:
- pickup
Date: 2018-06-13T10:00:00+09:00
URL: https://mackerel.io/ja/blog/entry/tech/high-loadavg-every-7-hours
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/17391345971652182166
---

Mackerelチームのエンジニアのid:itchyny:detailです。

「mackerel-agentを入れるとloadavgが7時間ごとに上昇する」

先日、このような問い合わせを複数のお客さまから受けました。私も実験してみたところ、確かに再現しました。EC2 t2.microにmackerel-agentを入れて簡単なログ監視とプロセス監視を設定し、数日放置しました。
[f:id:mackerelio:20180611212106p:plain]
確かに、約7時間ごとにloadavgが上昇しています。この周期のcronの設定はしておらず、またmackerel-agent内部でも7時間ごとに行う処理はありません。しかし、プラグインを多く入れるほどloadavgのピーク値も上がります。

本エントリーでは、この現象の原因について説明します。

loadavgが上昇する原因を調べるには、まずloadavg自体がどう計算されているかを知る必要があります。
まずは、Linuxがloadavgをどのように計算しているかを説明します。

Linuxのloadavgは、run queueにあるプロセス (running process) とディスクI/Oやロック待ちのプロセス (uninterruptible process) の総数の指数移動平均値です。
Linuxのプロセスには、以下のような状態があります。
```
 % man ps | grep -A 10 "^PROCESS STATE"
PROCESS STATE CODES
       Here are the different values that the s, stat and state output specifiers (header "STAT" or "S") will display to describe the state of a process:

               D    uninterruptible sleep (usually IO)
               R    running or runnable (on run queue)
               S    interruptible sleep (waiting for an event to complete)
               T    stopped, either by a job control signal or because it is being traced
               W    paging (not valid since the 2.6.xx kernel)
               X    dead (should never be seen)
               Z    defunct ("zombie") process, terminated but not reaped by its parent
```
この内の `R` と `D` の状態のプロセスの数を、指数加重平均で平滑化した値がloadavgとなります。

loadavgの対象となるrunningとuninterruptibleなプロセスの総数を、時刻 [tex:t] に対する関数として [tex:p(t)] と表しましょう。
サンプリング間隔を [tex: \Delta t]、時定数を [tex: T] とすると、プロセス数の指数移動平均、つまり指数関数的に減衰する加重平均は以下のように書くことができます [^1]。
[^1]: 係数の和が1であることを確かめることができます。加重平均であるための必要条件です。
$$
\begin{align}
L(t) &= \big(1 - e\^{-\Delta t / T}\big) \big( p(t) + e\^{-\Delta t / T} p(t - \Delta t) + e\^{-2\Delta t / T} p(t - 2 \Delta t) + \cdots \big)  \\\
&= \big(1 - e\^{-\Delta t / T}\big) p(t) + e\^{-\Delta t / T} L(t - \Delta t)
\end{align}
$$
これまでの時刻の [tex:p(t)] の指数移動平均値は、前の計算値 [tex:L(t-\Delta t)] と現在の値 [tex:p(t)] の加重平均値で表すことができます。
これまでのプロセス数の履歴を保持していなくても、前回のloadavgと現在のプロセス数から新しいloadavgを計算できるということです。

この漸化式をもとに、定数を適切に選んでシミュレーションした値 ([tex:\Delta t = 5, T=60, 300, 900]) と、実際のLinux (EC2 t2.micro上で `yes > /dev/null&` を実行し、10分後にそのプロセスを落とす) で計測された値を比較してみると、以下のような結果が得られました。
[f:id:mackerelio:20180608155746p:plain]

Linuxのソースコードを参照しながら、実際に上記の漸化式をもとにloadavgが計算されていることを確認します。
まずは [kernel/sched/loadavg.c](https://github.com/torvalds/linux/blob/29dcea88779c856c7dc92040a0c01233263101d4/kernel/sched/loadavg.c) を見てみましょう。
```c
/* Variables and functions for calc_load */
atomic_long_t calc_load_tasks;
unsigned long calc_load_update;
unsigned long avenrun[3];

void calc_global_load(unsigned long ticks)
{
	unsigned long sample_window;
	long active, delta;

	sample_window = READ_ONCE(calc_load_update);
	if (time_before(jiffies, sample_window + 10))
		return;

	// ...

	active = atomic_long_read(&calc_load_tasks);
	active = active > 0 ? active * FIXED_1 : 0;

	avenrun[0] = calc_load(avenrun[0], EXP_1, active);
	avenrun[1] = calc_load(avenrun[1], EXP_5, active);
	avenrun[2] = calc_load(avenrun[2], EXP_15, active);

	WRITE_ONCE(calc_load_update, sample_window + LOAD_FREQ);

	// ...
}
```
loadavgの値は `avenrun` に格納されています。
この値の更新を定期的に行うために、 `calc_load_update` という値を `LOAD_FREQ` ずつ増やしながら、 `jiffies` (1 tickごとに1増える、時刻を表す変数) と比較していることがわかります。
つまりloadavgは更新間隔は `LOAD_FREQ` ごとに行われています。
この値は [include/linux/sched/loadavg.h](https://github.com/torvalds/linux/blob/29dcea88779c856c7dc92040a0c01233263101d4/include/linux/sched/loadavg.h#L20) にかかれている通り、 `5*HZ+1` ごとです。
`HZ` は1秒に何tick増えるかという値ですから [^2]、5秒と1 tickごとにloadavgが更新されるということがわかります。
[^2]: このエントリーの `HZ` はカーネルのタイマー周波数 `CONFIG_HZ` であり、ユーザーランドの周波数 `USER_HZ` と区別する必要があります。 `getconf CLK_TCK` や `/proc/stat` から得られる値は `USER_HZ` であり、x86では100と決まっています。カーネルのタイマー周波数は `grep "CONFIG_HZ=" /boot/config-$(uname -r)` で調べるか、 `watch -n1 "grep ^jiffies /proc/timer_list | head -n1"` の増え方で見ることができます。

では、実際に値を計算している `calc_load` を見てみます。
```c
/*
 * a1 = a0 * e + a * (1 - e)
 */
static unsigned long
calc_load(unsigned long load, unsigned long exp, unsigned long active)
{
	unsigned long newload;

	newload = load * exp + active * (FIXED_1 - exp);
	if (active >= load)
		newload += FIXED_1-1;

	return newload / FIXED_1;
}
```
分岐の処理を無視して一つの式に書いてみます [^3]。
[^3]: 分岐の処理は、まったくプロセスがない状態で 0.0、プロセスが常に一つある状態で 1.0 となるようにするための処理です。この処理が全くなければいつまで経ってもloadavgが 1.0 になりませんし、分岐なしに常に足すという処理にしてしまうと、idle状態で 0.0 になりません。かつては 0.5 相当を足してから割ることで四捨五入になっていたのですが、idle状態で 0.0 にならない問題が指摘されて、プロセス数が増えているときだけ切り上げする形になっています。詳しくは[パッチ](https://github.com/torvalds/linux/commit/20878232c52329f92423d27a60e48b6a6389e0dd)や[メーリングリスト](https://lkml.org/lkml/2016/4/28/689)を参照してください。
```c
	avenrun[0] = ((FIXED_1 - EXP_1) * active + EXP_1 * avenrun[0]) / FIXED_1;
```
ここで [include/linux/sched/loadavg.h](https://github.com/torvalds/linux/blob/29dcea88779c856c7dc92040a0c01233263101d4/include/linux/sched/loadavg.h) によると `EXP_1 = 1884`, `FIXED_1 = (1<<11)` です。
また上の方で求めた漸化式に [tex:\Delta t=5, T = 60] を代入すると次のようになります。
$$
\begin{align}
L(t) = \big(1 - e\^{-1/12}\big) p(t) + e\^{-1/12} L(t - \Delta t)
\end{align}
$$
整数演算にするために11bitシフトした (`FIXED_1` 倍した) 値で計算していることと、[tex:e\^{-1/12}2^{11}] がおよそ1884であることを確かめると、確かに漸化式とLinuxで行われている計算が一致することがわかります。
loadavg5, 15も同様に確認できます。


loadavgはおよそ5秒ごとに、その瞬間のrun queue内のプロセス数 (`calc_load_tasks`) と漸化式を用いて更新されています。
正確には `5*HZ+1` tickごとに計算されています。
この `+1` が、約7時間ごとにloadavgが上昇する現象の原因です。

mackerel-agentは1分ごとにメトリックを収集するために、プラグインのプロセスを開いています。
loadavgの再計算が5秒ぴったりから少しだけずれているために、mackerel-agentがメトリック収集を始めるタイミングとloadavgの再計算のタイミングが定期的に一致するのです。

`HZ` が1000の環境では、loadavgは5.001秒ごとに更新されます。これを5000倍すると 25005秒 すなわち 6時間56分45秒 という周期で、`+1` の部分が溜まって `5*HZ` と一致します。この周期は、mackerel-agentのメトリック収集間隔である60秒の倍数ではありませんが、プロセスを開くタイミングとloadavgの更新が (0.003秒ずれますが) ほぼ重なる現象が6時間57分ごとに起きるのです。

`HZ` が250の環境では、`5*HZ+1` を1250回繰り返すと 6255秒 すなわち 1時間44分15秒 となります。この周期でloadavgが上がってもよいはずですが、mackerel-agentのメトリック収集タイミングと0.012秒ずつずれていくのが無視できません。60秒の倍数となるのはさらに4倍、つまり6時間57分周期で、mackerel-agentのメトリック収集のタイミングとぴったり重なります。私の検証環境では `HZ=250` でした。エントリー冒頭のグラフを見ると、確かに6時間57分の周期で高さの揃ったピークが立っていることがわかります。さらに、このピークの1時間44分前後で小さなピークが立っているところがあるのも見て取れます。これは `HZ` が250であることに由来する現象です。

loadavgの更新間隔はなぜぴったり5秒間隔の更新ではないのでしょうか。
Linuxの中では様々なプロセスが独自の周期で処理を行っています。
例えば5秒間隔で処理を行うようなプロセスがあると、loadavgの再計算タイミングとぴったり一致することが起こる可能性があります。
意図せずタイミングが合致してloadavgが上昇する現象が起きるのを防ぐために、周期を5秒から少しずらしているのです。
この経緯は、`5*HZ`に`1`足すことになった[パッチのコミットメッセージ](https://github.com/torvalds/linux/commit/0c2043abefacac97b6d01129c1123a466c95b7c1)や、[メーリングリスト](https://lkml.org/lkml/2007/10/2/325)を読むとわかります。

Linuxにおけるloadavgの更新間隔は、意図的に5秒から少しずれています。この少しのずれが溜まっていき、約6時間57分の周期で60秒の倍数となります。mackerel-agentは60秒ごとにプラグインのプロセスを開いており、このタイミングがloadavgの再計算タイミングと合う現象が周期的に起こります。これが、mackerel-agentを入れるとloadavgが定期的に上昇する現象の原因です。この現象は、[https://blog.avast.com/investigation-of-regular-high-load-on-unused-machines-every-7-hours:title=collectd]や[https://github.com/influxdata/telegraf/issues/3465:title=Telegraf]など、他のモニタリングツールでも報告されています。


## 参考文献
- [https://blog.avast.com/investigation-of-regular-high-load-on-unused-machines-every-7-hours:title=Avast Blog: Investigation of regular high load on unused machines every 7 hours]
  - 本現象について説明されたブログです。collectdを用いた環境で同じ現象が起きています。
- [http://www.brendangregg.com/blog/2017-08-08/linux-load-averages.html:title=Linux Load Averages: Solving the Mystery]
  - loadavgにuninterruptible processの数が計算に入っている理由が歴史的な経緯を追って説明されています。
