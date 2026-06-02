---
Title: ネットワーク監視をおこなう
Date: 2026-06-02T14:51:10+09:00
URL: https://mackerel.io/ja/docs/entry/howto/network-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032036129321
---

Mackerelでは公式プラグインや専用ツールを利用することで、以下のようなネットワーク監視ができます。

- ホストやネットワーク機器へのping到達性
- SNMPで取得できるネットワーク機器のメトリック（インターフェースの通信量など）

少数のホストや機器を監視する場合は mackerel-agent と公式プラグインで対応できます。多数のネットワーク機器を集約して監視したい場合は、[Mackerel Labs](https://github.com/mackerelio-labs) で提供している sabatrafficd / sabapingd を利用できます。

このページでは、プラグインや専用ツールの導入から監視内容に応じたシンプルな設定までをご案内します。詳細な設定については、利用するプラグインやツールのヘルプをご確認ください。

[:contents]

## ネットワーク監視の始め方

### 1. 公式プラグイン集をインストールする

ping応答を監視する `check-ping` は公式チェックプラグイン集、SNMPでメトリックを取得する `mackerel-plugin-snmp` は公式メトリックプラグイン集にそれぞれ同梱されています。利用したい監視に応じて、以下のページを参照しプラグイン集を導入してください。

- [チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins)
- [公式メトリックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins)

Windows Server をお使いの場合、これらのプラグインは Windows 版の mackerel-agent に同梱されています。

### 2. 監視の設定を追加する

check-ping は監視を実行するサーバから対象ホストへping応答を確認し、mackerel-plugin-snmp はサーバからネットワーク機器へSNMPによる問い合わせをおこないます。いずれのプラグインも、監視を実行するサーバの mackerel-agent.conf の末尾に、監視したい内容に合わせて以下の設定を追加します。

追記が完了したら、mackerel-agent を再起動（Linux の場合は reload でも可）することで、監視が開始されます。

以下に監視したい内容別の設定例を示します。

#### ping応答の監視（check-ping）

`check-ping` を利用すると、指定したホストや機器へのping到達性を監視できます。

以下は、`check-ping-sample` という監視ルール名で、`192.168.1.1` に5回パケットを送信し、1回も100ms以内に応答がなければCRITICALアラートを発報する例です。

```toml
[plugin.checks.check-ping-sample]
command = ["check-ping", "-H", "192.168.1.1", "-n", "5", "-w", "100"]
```

<figure class="figure-image figure-image-fotolife" title="ホスト詳細画面の Monitors に check-ping-sample という名前のチェック監視が表示されている様子"><div class="images-row mceNonEditable">[f:id:mackerelio:20260528170046p:plain][f:id:mackerelio:20260528170042p:plain]</div><figcaption>ホスト詳細画面の Monitors に check-ping-sample という名前のチェック監視が表示されている様子</figcaption></figure>

そのほか応答時間のしきい値などの設定も可能です。詳しくは [チェックプラグイン - check-ping - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-ping) をご確認ください。

#### ネットワーク機器のメトリック取得（mackerel-plugin-snmp）

`mackerel-plugin-snmp` を利用すると、SNMPに対応したスイッチやルーターなどのネットワーク機器から任意のMIB情報を取得し、メトリックとして投稿できます。

以下は、コミュニティ名 `public` で `192.168.1.1` のSNMPエージェントに問い合わせ、OID `.1.3.6.1.2.1.2.2.1.10.1`（インターフェースのうちインデックス番号1のifInOctets）を `ifInOctets` というメトリック名で、カウンター値の差分として投稿する例です。

```toml
[plugin.metrics.snmp]
command = ["mackerel-plugin-snmp", "-name", "interface", "-host", "192.168.1.1", "-community", "public", ".1.3.6.1.2.1.2.2.1.10.1:ifInOctets:1:0:uint32"]
```

そのほか取得するOIDやグラフ名、単位の指定なども可能です。詳しくは [メトリックプラグイン - mackerel-plugin-snmp - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-snmp) をご確認ください。

### 3. チェック監視の動作を変更する

必要に応じて、チェック監視の実行間隔や、何回連続で異常を検知したらアラート発報するかなどの設定が可能です。
以下はデフォルトでは実行間隔が1分のチェック監視を5分間隔に変更し、さらに3回実行した結果がすべて異常だった場合にアラートを発報する例です。

```toml
[plugin.checks.check-ping-sample]
command = ["check-ping", "-H", "192.168.1.1", "-n", "5", "-w", "100"]
check_interval = 5m
max_check_attempts = 3
```

そのほか、チェック監視に設定可能な項目については、[チェック監視項目を追加する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/custom-checks#items) をご参照ください。

## 多数のネットワーク機器を集約して監視する

mackerel-agentと公式プラグインの組み合わせは、機器ごとにエージェントの設定をおこなう必要があり、機器の数が増えるとポーリングタイミングが集中してスケールしづらくなります。多数のネットワーク機器を効率よく監視したい場合は、Mackerel Labsで提供している sabatrafficd / sabapingd の利用を検討してください。

sabatrafficd / sabapingd はどちらも以下のような特徴を持ちます。

- mackerel-agentとは別のツールで、監視サーバ上で常駐します。mackerel-agentと同じホストに同居させて動作させることもできます
- ネットワーク機器1台ごとにMackerel上のホストを事前に作成しておく必要があります（後述）
- ポーリングのタイミングを分散することで多数の機器に対応できます
- Mackerelへの投稿エンドポイントに接続できないときには、メモリ（および設定すればディスク）にキャッシュし、接続復帰後に順次再送します

インストール用のLinux向けdeb / rpmパッケージは、各リポジトリのReleasesページで配布されています。なお、Windows版は提供していません。

インストール後は設定ファイルの雛形が `/etc/sabatrafficd/sabatrafficd.yaml`（sabapingdの場合は `/etc/sabapingd/sabapingd.yaml`）に作成されるので、これを編集したうえで `systemctl enable --now sabatrafficd` のように起動してください。

### 投稿先ホストの事前作成

sabatrafficd / sabapingdは、機器のメトリックをMackerel上のホストに紐付けて投稿します。投稿先のホストは事前に [CLIツール mkr](https://mackerel.io/ja/docs/entry/advanced/cli) などで作成しておき、設定ファイルにそのホストIDまたはcustom identifierを記載します。

```console
MACKEREL_APIKEY=<APIキー> mkr create --customIdentifier <custom-identifier-name> <hostname>
```

### sabatrafficd（SNMPによるメトリック収集）

[sabatrafficd](https://github.com/mackerelio-labs/sabatrafficd) は、SNMPでネットワーク機器からインターフェースごとの通信量やエラーパケット数などを取得し、Mackerelのメトリックとして投稿するツールです。

主な特徴は以下の通りです。

- インターフェース（ポート）は自動認識されます。インターフェースごとに以下のメトリックを取得します
  - 通信量（`ifHCInOctets` / `ifHCOutOctets`）
  - エラーパケット数（`ifInErrors` / `ifOutErrors`）
  - 廃棄パケット数（`ifInDiscards` / `ifOutDiscards`）
- 通信量についてはシステムメトリックとして投稿されるため、ホストの死活監視（connectivity）の対象になります
- CPU負荷やファン速度など、固有のMIBを取得したい場合は、OIDを指定することで任意のメトリックとして投稿できます
- 対象インターフェースの絞り込み（include / exclude）や、SNMP v2c / v3の選択が可能です

設定ファイル（`config.yaml`）の例は以下の通りです。

```yaml
x-api-key: XXXXX
collector:
  - host-id: xxxxx
    community: public
    host: 192.0.2.1
    mibs:
      - ifHCInOctets
      - ifHCOutOctets
      - ifInDiscards
      - ifOutDiscards
      - ifInErrors
      - ifOutErrors
    skip-linkdown: true
  - host-id: yyyyy
    community: public
    host: 192.0.2.2
    mibs:
      - ifHCInOctets
      - ifHCOutOctets
```

<figure class="figure-image figure-image-fotolife" title="sabatrafficd で取得したネットワーク機器のインターフェース通信量グラフ">[f:id:mackerelio:20260528170438p:plain]<figcaption>sabatrafficd で取得したネットワーク機器のインターフェース通信量グラフ</figcaption></figure>

詳しくは [sabatrafficd - GitHub](https://github.com/mackerelio-labs/sabatrafficd) のREADMEをご確認ください。

### sabapingd（ICMPによる疎通監視）

[sabapingd](https://github.com/mackerelio-labs/sabapingd) は、対象の機器に対してpingを試行し、レイテンシーとパケットロスをMackerelのメトリックとして投稿するツールです。

主な特徴は以下の通りです。

- 3回のping試行結果から、レイテンシー（最大・最小・平均）とパケットロスをメトリック化します。レイテンシーは平均のみの投稿に切り替えることもできます
- メトリックとして傾向を可視化できるため、アラートしきい値の見積もりがしやすくなります

設定ファイル（`config.yaml`）の例は以下の通りです。

```yaml
x-api-key: XXXXX
privileged: false
collector:
  - host-id: xxxxx
    host: 192.0.2.1
  - host-id: yyyyy
    host: 192.0.2.2
    always-send-packetloss: true
```

パケットロスが0のときには既定でメトリックが投稿されません。継続的に投稿したい場合は、上記の例のように `always-send-packetloss` オプションを利用できます。

<figure class="figure-image figure-image-fotolife" title="sabapingd で取得したネットワーク機器のレイテンシーのグラフ">[f:id:mackerelio:20260528171109p:plain]<figcaption>sabapingd で取得したネットワーク機器のレイテンシーのグラフ</figcaption></figure>

詳しくは [sabapingd - GitHub](https://github.com/mackerelio-labs/sabapingd) のREADMEをご確認ください。
