---
Title: Mackerel Advent Calendarを始めます & mackerel-agent.confに関するTips
Date: 2015-12-01T17:37:09+09:00
URL: https://mackerel.io/ja/blog/entry/advent-calendar2015/day1
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/6653586347147077050
---

Mackerelディレクターの [@songmu](https://twitter.com/songmu) です。師走に入り、界隈ではtech advent calendarが活況ですが、Mackerelでも[Mackerel Advent Calendar](http://qiita.com/advent-calendar/2015/mackerel)を実施します。

どんな記事が集まるか楽しみです。まだ埋まっていない日もあるので、なにか書くネタがある方は以下からエントリーをお待ちしています。

http://qiita.com/advent-calendar/2015/mackerel

## mackerel-agent.confに関するTips

さて本題です。mackerel-agentの設定ファイルであるmackerel-agent.confは [TOML](https://github.com/toml-lang/toml) 形式で指定するのはご存知かと思います。

mackerel-agent.conf にはプラグインの設定を書く場合、設定が長くなってしまい改行を入れたくなることもあるかと思います。そういったケースでは、TOMLのMulti-line literal stringsが便利です。

例えば、ログ監視プラグインである、check-logの設定などは以下のように書けます。

```config
[plugin.checks.access_status]
command = '''
  check-log                                     \
    --file /var/log/nginx/access.log            \
    --pattern 'HTTP/1\.[01]" [45][0-9][0-9] '   \
    --exclude 'GET .*?robots\.txt HTTP/1\.[01]' \
    --warning-over 3 --critical-over 10         \
    --return
'''
```

これは以下の様な設定になっています。

- Nginxのアクセスログ(combined形式)を見て
- 4xx, 5xx系のレスポンスをマッチ
- robots.txt へのアクセスは除外して
- 1分間に3行以上マッチしたらWarning
- 1分間に10行以上マッチしたらCritical
- マッチした行の内容をMackerelに送信する

改行を入れることでぐっと読みやすくなっているんじゃないでしょうか。改行位置の `\` はmackerel-agent内部でシェルに改行のエスケープを渡すために必要です。

ちなみに、上記のログ監視の設定は実際に僕自身の個人サービスで動かしているものです。アラートが上がるとMackerel画面上で下記のように変なアクセスがあることが可視化されて面白いですね。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151201/20151201144607_original.png)

## まとめ

今回はログ監視を例に取り上げましたが、ログ監視を含めたチェック監視機能に関しては現在プラグインを鋭意拡充しているところです。開発はGitHub上の以下のURLで行なってますので、追加したい機能がありましたらpull requestもお待ちしています。

https://github.com/mackerelio/go-check-plugins

明日のAdvent Calenderは、Mackerel開発チームの id:daiksy が書く予定です。お楽しみに。

## 参考

- [スクリプトによるチェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)
- [チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins)
- [ログ監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/log)
- [プロセス監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/process)
- [TCPサーバーの確認を行う](https://mackerel.io/ja/docs/entry/howto/check/tcp)
