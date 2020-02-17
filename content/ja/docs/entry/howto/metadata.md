---
Title: メタデータを利用する
Date: 2017-02-24T10:43:28+09:00
URL: https://mackerel.io/ja/docs/entry/howto/metadata
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687220617062
---

<h2 id="metadata">ホストメタデータ</h2>
各ホストに任意のJSONデータを登録できます。運用上の管理データやパッケージのバージョンなど、様々な情報を登録してご利用いただけます。

mackerel-agentに設定を追加してメタデータを投稿する方法と、APIから投稿する方法があります。
APIから投稿したり取得する方法は、以下のAPIドキュメントを参照してください。
[https://mackerel.io/ja/api-docs/entry/metadata:embed:cite]

<h3 id="setting">設定</h3>
この項ではmackerel-agentからメタデータを投稿する方法を説明します。
[mackerel-agentの設定ファイル](https://mackerel.io/ja/docs/entry/spec/agent#config-file)に、以下のような項目を追加します。
このサンプルは、インストールされているDebianパッケージのバージョンとアーキテクチャをホストのメタデータとして登録するものです。

```config
[plugin.metadata.packages]
command = ["perl", "/path/to/packages.pl"]
execution_interval = 60
env = { SAMPLE_KEY = "VALUE" }
```

- 項目名: `plugin.metadata.`で始まっている必要があります。上のサンプルで`packages`に該当する部分はユーザーが任意に決めることができ、[ホストメタデータAPI](https://mackerel.io/ja/api-docs/entry/metadata)のネームスペース (namespace) に該当します。
- `command`: mackerel-agentから実行されるコマンドを指定します。このコマンドは標準出力にJSONを出力する必要があります。
- `execution_interval`: コマンドを実行する間隔を分で指定します。エージェントのバージョンがv0.67.0以降であれば`"10m"`や`"1h"`のような表現でも記述できます。この設定は省略可能で、省略した場合は10分間隔で実行します。最小実行間隔は10分です。
- `env`: commandに渡す環境変数を指定できます。TOMLの[Table][]もしくは[Inline Table][]で指定します。


packages.plのサンプルです。
```perl
#!/usr/bin/env perl
use 5.014;
use warnings;
use utf8;
use JSON::PP qw/encode_json/;

my @lines = split /\n/, `dpkg --list`;
my %packages;
for my $line (@lines) {
    my ($name, %info) = parse_package($line);
    next unless %info;
    $packages{$name} = \%info;
}
say encode_json \%packages;

sub parse_package {
    my $line = shift;
    my @items = split /\s+/, $line;
    return unless $items[0] eq 'ii';
    return $items[1], (
        version      => $items[2],
        architecture => $items[3],
    );
}
```

投稿したJSONデータは、APIを用いて取り出せます。上記のサンプルでは`<namespace>`のところが`packages`となります。
```sh
curl -XGET https://api.mackerelio.com/api/v0/hosts/<hostId>/metadata/<namespace> -H 'X-Api-Key:<APIKEY>'
```
詳しくは[ホストメタデータAPI](https://mackerel.io/ja/api-docs/entry/metadata)を参照してください。

[Table]: https://github.com/toml-lang/toml#table
[Inline Table]: https://github.com/toml-lang/toml#inline-table
