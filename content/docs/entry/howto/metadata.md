---
Title: Using metadata
Date: 2017-02-27T16:25:10+09:00
URL: https://mackerel.io/docs/entry/howto/metadata
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687221675986
---

<h2 id="metadata">Host metadata</h2>
You can register arbitrary JSON data to each host. Various information such as operational management data and package version can be registered and used.

Metadata can be posted by adding configurations to mackerel-agent or by using the API. Please refer to the API document below for information on how to post and obtain from the API. 
[https://mackerel.io/api-docs/entry/metadata:embed:cite]

<h3 id="setting">Configuration</h3>

This section explains how to post metadata from mackerel-agent. Add the following items to the [mackerel-agent configuration file](https://mackerel.io/docs/entry/spec/agent#config-file). This sample registers the version and architecture of the installed Debian package as host metadata.

```toml
[plugin.metadata.packages]
command = ["perl", "/path/to/packages.pl"]
execution_interval = 60
env = { SAMPLE_KEY = "VALUE" }
```

- Item name: This must begin with `plugin.metadata.`. In the sample above, the section corresponding to packages can be decided arbitrarily by the user and corresponds to the namespace of the [Host metadata API](https://mackerel.io/api-docs/entry/metadata).
- `command`: Specifies the command to be executed from mackerel-agent. This command must output JSON to standard output.
- `execution_interval`: Specifies the command execution interval in minutes. If the agent version is v0.67.0 or later, expressions such as `"10m"` or `"1h"` can also be written. This setting can be omitted. If omitted, the command will execute every 10 minutes. The minimum interval is 10 minutes.
- `env`: Environment variables can be specified to pass to command. Specify with TOML [Table][] or [Inline Table][].


This is a sample of packages.pl.

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

Posted JSON data can be retrieved using the API. In the above example, `packages` corresponds to `<namespace>`.
```sh
curl -XGET https://api.mackerelio.com/api/v0/hosts/<hostId>/metadata/<namespace> -H 'X-Api-Key:<APIKEY>'
```
For more details, refer to the [Host metadata API document](https://mackerel.io/api-docs/entry/metadata).

[Table]: https://github.com/toml-lang/toml#table
[Inline Table]: https://github.com/toml-lang/toml#inline-table
