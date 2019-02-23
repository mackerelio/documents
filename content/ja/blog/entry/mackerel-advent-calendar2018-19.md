---
Title: mackerel-agentのコマンド実行と配列指定のススメ
Date: 2018-12-19T19:54:31+09:00
URL: https://mackerel.io/ja/blog/entry/mackerel-advent-calendar2018-19
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/10257846132687209858
---

Mackerelプロダクトマネージャーの id:Songmu:detail です。この記事は、[Mackerel Advent Calendar 2018](https://qiita.com/advent-calendar/2018/mackerel) の19日目の記事です。

さて、ご存知の通り、[mackerel-agent](https://github.com/mackerelio/mackerel-agent)のプラグイン実行やアクション実行はコマンドライン形式の文字列で記述します。例えば以下のような形です。

```
[plugin.metrics.accesslog]
command = "mackerel-plugin-accesslog /etc/nginx/access.log"
```

ターミナルで試したコマンドをそのままmackerel-agent.confに書けるのでわかりやすいですね。

## コマンドの配列指定とそれのススメ

実は、この `command` ですが、文字列の他に配列で指定することも可能です。例えば以下のような具合です。

```
[plugin.metrics.accesslog]
command = ["mackerel-plugin-accesslog", "/etc/nginx/access.log"]
```

結論から言うと、少し記述量が増えてはしまいますが、文字列にする必要性が無い限り、 **配列指定を使う方がおすすめ** です。これは、以下のpull requestで後から追加された機能で、Mackerelリリース当初にはない機能でした。

[https://github.com/mackerelio/mackerel-agent/pull/293:embed:cite]

文字列指定と配列指定で何が違うのでしょうか。

## 文字列指定だとシェル経由になる

文字列指定だとコマンドはシェル経由の実行となるというのが答えです。具体的には、Linuxの場合 `sh -c` に、Windowsの場合 `cmd /c` (または `cmd /U /c`) に文字列が渡されてコマンドが実行されます。

文字列で設定されている以上、実装としてはシェル経由になるのは致し方無く、逆にシェルの機能が使える利点があるとも言えます。つまり以下のような機能が使えます。

- globや環境変数の展開がおこなわれる
- パイプやリダイレクションが使える

普段普通にプラグインを指定している分には、あまり意識しないかもしれませんが、例えばファイルの存在チェックをしたいのであれば、以下のようにシェルコマンドを書いてチェックすることができるのです。

```
[plugin.checks.test-file]
command="test -f $HOME/path/to/file || (echo 'ooooops' && exit 2)"
```

それどころか、TOMLの複数行リテラル文字列を使えば、以下のようにシェルスクリプトっぽいものを書くことすらできます。各行末を `;` で区切るのがポイントです。

```
[plugin.checks.warning]
command='''
    h=$(hostname);
    echo $h;
    exit 1;
'''
```

## 「シェル経由」の怖さ

とはいえ、Webエンジニアであれば「シェル経由」と聞くとなんとなく嫌な気持ちになりますよね。単なる設定ファイル内の記述であり、ユーザー入力を受け付けるわけでも無いため、OSコマンドインジェクションなどのリスクは少なそうですが、やはり前項のようにあまりにも自由度が高いと自分の足を撃たないか心配にはなるでしょう。

複数のコマンドを入れ込む例について前の項で説明しましたが、実際にはあまりそういう曲芸のようなことはやりすぎず、スクリプトファイルに落としてそのファイルをcommandに指定する方が良いでしょう。

また、globや環境変数の展開がおこなわれるのが便利な半面、逆に特殊文字のエスケープにも気を遣う必要があります。特に、check-logなどは気をつけないといけません。例えば以下の設定は誤っています。

```
[plugin.checks.log]
command = "check-log --file /path/to/*.log --pattern ERROR"
```

check-logの--fileオプションはglob指定を受け付ける機能がありますが、上の指定だと、check-logに`*`が受け渡される前にシェルで展開されてしまいます。正しくは以下のように `/path/to/*.log` の部分のクオートが必要です。

```
[plugin.checks.log]
command = "check-log --file '/path/to/*.log' --pattern ERROR"
```

ここではLinuxの例を出しましたが、Windowsにおける文字列のエスケープは異常に難解なことで知られており、正しくエスケープをするのはかなり難しいという話もあったりします。以下のエントリに詳しいです。

[https://thinca.hatenablog.com/entry/20100210/1265813598:embed:cite]

## 文字列指定のもう一つの問題点

文字列指定の場合もう一つちょっとした問題点があります。コマンド実行がシェル経由になるということは、そのコマンド自体は孫プロセスになる(ことがある)ということです。

正確に言うとシェルがよしなに子プロセスにしてくれることもあるのですが、それについてはここでは触れません。以下のエントリを参考にしてください。

[http://www.songmu.jp/riji/entry/2018-12-19-dash-bash-exec.html:title]

実際問題、孫プロセスになることで困ることはforkが二重になるパフォーマンスの極々僅かの劣化以外には大きな実害はありません。しかし、一点密かに困る点があります。

mackerel-agentはプラグインの実行に時間がかかりすぎた場合、プラグインの実行プロセスにシグナルを送ってタイムアウトさせる機能があります。チェックプラグインの場合アラートが発生しますが、実はmackerel-agentのプラグインは、そのシグナルをトラップすることでタイムアウトエラーを回避することができます。これは以下のpull requestで機能追加されています。

[https://github.com/mackerelio/mackerel-agent/pull/476:embed:cite]

しかし、プラグインが実行されているコマンドが、mackerel-agentの孫プロセスとして起動されてしまった場合、そのプラグインがシグナルをハンドリングしたとしても、mackerel-agentはそれを察知できないため、意図せずタイムアウトが発生してしまいます。

`command` を文字列指定ではなく配列指定にすれば子プロセスとして実行されるため、この問題が起こることはありません。

現状公式に配布しているプラグインで、シグナルハンドリングするものはありませんが、例えば、仕組み上タイムアウトが発生しがちな `check-log` ではその機構を入れたいとは考えています。(pull requestをお待ちしています…!)

前述の通り、`check-log`はglobの展開周りに纏わるエスケープの難しさもあるため、`check-log`の引数指定は配列指定をするのを特に推奨します。

## まとめ

- mackerel-agentのプラグインのcommand設定には文字列指定と配列指定があります
- 文字列指定よりも、丁寧に配列指定する方がオススメです
- 文字列指定は便利な面もありますが用法用量に注意して適切にご利用ください
