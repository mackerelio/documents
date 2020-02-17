---
Title: コンテナを監視する
Date: 2018-09-18T19:19:33+09:00
URL: https://mackerel.io/ja/docs/entry/howto/container-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132634273848
---

Amazon ECSやKubernetesなど、コンテナオーケストレーションプラットフォームにおけるコンテナの監視にはmackerel-container-agentが利用できます。

## 特徴

mackerel-container-agentにはつぎのような特徴があります。

- コンテナオーケストレーションプラットフォームを対象とした専用の監視エージェントです
- Dockerイメージで提供します
- タスク(ECS)やPod(Kubernetes)を監視します
- タスク/Podのサイドカーとして実行します
- タスク/Podをホストとして扱い、サービス、ロールを割り当てることが可能です
- エージェント終了時に自動退役します

以下に挙げるものはmackerel-container-agentにおいて**監視対象外**となります。

- コンテナホストの監視
  - コンテナホストのCPUやメモリ、ネットワークなど
- コンテナオーケストレーションプラットフォーム自体の監視
  - ノードやタスク, Pod数など
- サポートするコンテナオーケストレーションプラットフォーム以外のコンテナの監視

#### 課金に関する注意事項

mackerel-container-agentを利用すると、1つのタスクやPodに対して1つのホストがMackerelに登録されます。有料プランの場合は課金が発生しますのでご注意ください。詳しくは[FAQ・ホスト数の計算方法について](https://mackerel.io/ja/docs/entry/faq/contracts/calculate-host-number)をご覧ください

## サポートするコンテナオーケストレーションプラットフォーム

mackerel-container-agentでサポートするコンテナオーケストレーションプラットフォームはつぎのとおりです。

- Amazon Elastic Container Service(ECS)
  - EC2起動タイプ
  - Fargate起動タイプ
  - **Windowsコンテナは対象外となります**
- Kubernetes

## セットアップ

mackerel-container-agentのセットアップ手順です。
各コンテナオーケストレーションプラットフォームごとに手順が異なります。

- [ECS](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecs)
- [Kubernetes](https://mackerel.io/ja/docs/entry/howto/install-agent/container/kubernetes)
- [EKS on Fargate(ベータ版機能)](https://mackerel.io/ja/docs/entry/howto/install-agent/container/eks-on-fargate)

mackerel-container-agent v0.1.0以降では、下記の手順は非推奨となります。
ご注意ください。

- [(非推奨) ECS(default, bridge, host, noneネットワークモード)](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsbasic)
- [(非推奨) Fargate, ECS(awsvpcネットワークモード)](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsawsvpc)
- [(非推奨) ECS(起動タイプ、ネットワークモード問わず)](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsv3)

## 取得メトリック

mackerel-container-agentで取得するメトリックはつぎのとおりです。
コンテナオーケストレーションプラットフォームによって取得内容が異なる場合があります。

| メトリック | 説明 | メトリック名 |
| :-- | :-- | :-- |
| CPU Usage | コンテナごとのCPU使用量(1コア=100%) | container.cpu.&lt;container_name&gt;.usage |
| CPU Limit | コンテナごとのCPU Limit(1コア=100%)。<br>Kubernetesではコンテナごとの `resources.limits.cpu` の値。定義がない場合はホストのコア数。<br>ECS, FargateではタスクCPU。定義がない場合はホストのCPUコア数。 | container.cpu.&lt;container_name&gt;.limit |
| Memory Usage | コンテナごとのメモリ使用量 | container.memory.&lt;container_name&gt;.usage |
| Memory Limit | コンテナごとのMemory Limit。<br>Kubernetesではコンテナごとの `resources.limits.memory` の値。定義がない場合はホストの搭載メモリ量。<br>ECS, Fargateではコンテナのメモリ制限。定義がない場合はタスクメモリ。コンテナ、メモリともに定義がない場合はホストの搭載メモリ量。 | container.memory.&lt;container_name&gt;.limit |
| Interface Rx | タスク/Podのインターフェースごとの受信バイト数。<br>ECS(default, bridgeネットワークモード)ではコンテナごとのインターフェースの受信バイト数。 | interface.&lt;container_name&gt;.rxBytes.delta |
| Interface Tx | タスク/Podのインターフェースごとの送信バイト数。<br>ECS(default, bridgeネットワークモード)ではコンテナごとのインターフェースの送信バイト数。| interface.&lt;container_name&gt;.txBytes.delta |

### ロールメトリックについて

ロールグラフではCPU, MemoryともにタスクやPodに属するコンテナのメトリックの合計値を表示します。

## 取得メタデータ

mackerel-container-agentでは各コンテナオーケストレーションプラットフォームの情報を取得し、ホストメタデータとして登録します。

- ECS, Fargate
  - クラスタ名, ARN, タスクリソースリミット, コンテナ名, DockerID など
- Kubernetes
  - Pod名, 名前空間, ラベル, コンテナ名, コンテナリソースリミット など

## エージェントの設定

エージェントの設定は環境変数、設定ファイルで行います。
プラグインを使わない場合は環境変数の設定のみでエージェントを利用できます。

### 環境変数による設定

| 変数名 | 説明 |
| :-- | :-- |
| MACKEREL_APIKEY | エージェントがMackerelサービスとの通信の際に用いる API キーを指定します。 |
| MACKEREL_APIBASE | Mackerel APIのエンドポイントを指定します (デフォルト: `https://api.mackerelio.com/`)。 |
| MACKEREL_ROLES | タスク、Podへサービス、ロールを設定できます。 |
| MACKEREL_AGENT_CONFIG | エージェントの設定ファイルを設定できます。こちらの詳細は後述します。 |
| MACKEREL_AGENT_CONFIG_POLLING_DURATION_MINUTES | エージェントの設定ファイルの変更を検知するために取得する間隔を分で指定します。|
| MACKEREL_IGNORE_CONTAINER | 監視を除外するコンテナの名前を正規表現で設定します。 |
| MACKEREL_HOST_STATUS_ON_START | 設定すると、エージェント起動時にホストのステータスを指定した値に変更します。 有効な値は "standby", "working", "maintenance", "poweroff" のいずれかです。|
| HTTP_PROXY(HTTPS_PROXY) | エージェントがコンテナ外部への通信に利用する HTTP Proxy を設定します。http probe (後述)の proxy とは別の設定となるので注意してください。 |

### 設定ファイルによる設定

エージェントの設定ファイルのファイルパスは環境変数 `MACKEREL_AGENT_CONFIG` で設定します。
`MACKEREL_AGENT_CONFIG` の指定がない場合はデフォルト設定で動作します。

`MACKEREL_AGENT_CONFIG` に指定可能なパスは次の通りです。

- ファイルパス: `/etc/mackerel/mackerel.yaml`
- HTTP/HTTPS: `https://example.com/mackerel/conf/mackerel.yaml`
- Amazon S3: `s3://bucket/mackerel/conf/mackerel.yaml`

設定ファイルはYAML形式で記述します。以下は設定ファイルの例です。

```
apikey: "YOUR_MACKEREL_APIKEY"
roles:
  - "My-Service:app"
  - "Another-Service:db"
ignoreContainer: '\Amackerel-container-agent\z'
```

デフォルトでは、設定ファイルは起動時に一度だけ読み込まれます。 `MACKEREL_AGENT_CONFIG_POLLING_DURATION_MINUTES` を設定することで、定期的に設定ファイルを取得して、変更を検知して適用します。

#### 設定項目

| 項目名 | 説明 |
| :-- | :-- |
| apikey | エージェントがMackerelサービスとの通信の際に用いる API キーを指定します。 |
| apibase | Mackerel APIのエンドポイントです (デフォルト: `https://api.mackerelio.com/`)。 |
| roles | タスク、Podへサービス、ロールを設定できます。 |
| ignoreContainer | 監視を除外するコンテナの名前を正規表現で設定します。 |
| root | mackerel-container-agentのルートディレクトリを指定できます (デフォルト: `/var/tmp/mackerel-container-agent`)。 |
| plugin.metrics | 任意のメトリックの取得、投稿するプラグインを設定できます。こちらの詳細は後述します。 |
| plugin.checks | 任意のチェック監視を実行、その結果を投稿するプラグインを設定できます。こちらの詳細は後述します。 |
| readinessProbe | Readiness Probeを設定できます。こちらの詳細は後述します。 |

### 設定の優先順位

環境変数、設定ファイルで同じ項目が定義されていた場合、***設定ファイルの値を優先*** します。ご注意ください。

### プラグインの利用

メトリックプラグイン、チェックプラグインが利用可能です。

また、mackerel-container-agentでは[公式プラグインを同梱したDockerイメージ](https://hub.docker.com/r/mackerel/mackerel-container-agent/tags)も公開しています。
`plugins` は `latest` に、`vX.Y.Z-plugins` は `vX.Y.Z` にプラグインを同梱したイメージとなります。

こちらのイメージに同梱されているプラグインはつぎのとおりです。

- [mackerel-agent-plugins](https://github.com/mackerelio/mackerel-agent-plugins)
  - mackerel-plugin-apache2
  - mackerel-plugin-elasticsearch
  - mackerel-plugin-fluentd
  - mackerel-plugin-gostats
  - mackerel-plugin-haproxy
  - mackerel-plugin-jmx-jolokia
  - mackerel-plugin-memcached
  - mackerel-plugin-mysql
  - mackerel-plugin-nginx
  - mackerel-plugin-php-apc
  - mackerel-plugin-php-fpm
  - mackerel-plugin-php-opcache
  - mackerel-plugin-plack
  - mackerel-plugin-postgres
  - mackerel-plugin-redis
  - mackerel-plugin-sidekiq
  - mackerel-plugin-snmp
  - mackerel-plugin-squid
  - mackerel-plugin-uwsgi-vassal
- [mackerel-plugin-json](https://github.com/mackerelio/mackerel-plugin-json)
- [go-check-plugins](https://github.com/mackerelio/go-check-plugins)
  - check-cert-file
  - check-elasticsearch
  - check-file-age
  - check-file-size
  - check-http
  - check-jmx-jolokia
  - check-log
  - check-memcached
  - check-mysql
  - check-postgresql
  - check-redis
  - check-ssh
  - check-ssl-cert
  - check-tcp

同梱されてるプラグイン以外を利用する場合は、[mackerel/mackerel-container-agent](https://hub.docker.com/r/mackerel/mackerel-container-agent)をベースイメージとして、利用したいプラグインをインストールしたイメージを用意してください。

#### 利用可能なプラグイン設定

mackerel-container-agentで利用可能な設定は以下のとおりです。

メトリックプラグインの設定項目 (`plugin.metrics`)

- **command**: エージェントが定期的に実行するコマンドです (必須項目)。
- **user**: コマンドの実行ユーザーです。指定がない場合はmackerel-container-agentの実行ユーザーとなります。
- **env**: コマンドに渡す環境変数です。
- **timeoutSeconds**: コマンドのタイムアウト時間を秒で指定します。デフォルト値は30秒です。

チェックプラグインの設定項目 (`plugin.checks`)

- **command**: エージェントが定期的に実行するコマンドです (必須項目)。
- **user**: コマンドの実行ユーザーです。指定がない場合はmackerel-container-agentの実行ユーザーとなります。
- **env**: コマンドに渡す環境変数です。
- **timeoutSeconds**: コマンドのタイムアウト時間を秒で指定します。デフォルト値は30秒です。
- **memo**: チェック監視に対してメモを設定できます。ここで指定した文字列は、アラート通知・アラート詳細画面・ホスト詳細ページで確認できます。

#### プラグイン設定例

以下はプラグインの設定例です。

```
plugin:
  metrics:
    mysql:
      command: mackerel-plugin-mysql

    redis6379:
      command: mackerel-plugin-redis -port=6379 -timeout=5 -metric-key-prefix=redis6379
      timeoutSeconds: 50

    sample:
      command: ruby /usr/local/bin/sample-plugin.rb
      user: "sample-user"
      env:
        FOO: "FOO BAR"
        QUX: 'QUX QUUX'

  checks:
    procs:
      command: "check-procs --pattern=/usr/sbin/sshd --warning-under=1"
      user: "sample-user"
      env:
        FOO: FOO BAR
      timeoutSeconds: 45
      memo: "check procs memo"
```

### Readiness Probeの設定
エージェントの起動時に、アプリケーションが正常に起動しているかの確認を行えます。この確認が成功した後に、ホストが作成されます。

mackerel-container-agentで設定できるReadiness Probeは以下の三種類です。いずれか一つのみ設定可能です。

- exec probe
  - コマンドを実行して、正常終了するかを確認します
- http probe
  - HTTPリクエストを送信して、正常に応答するかを確認します
- tcp probe
  - TCPによる接続ができるかを確認します

共通の設定項目 (`readinessProbe`)

- **timeoutSeconds**: コマンドやHTTPリクエストのタイムアウト時間を秒で指定します。デフォルト値は1秒です。
- **initialDelaySeconds**: エージェントが起動してからprobeのチェックを行うまでの時間を秒で指定します。デフォルト値は0秒です。
- **periodSeconds**: probeのチェックを行う間隔を秒で指定します。デフォルト値は10秒です。

#### exec probe
コマンドを実行し、終了ステータスが0のときに正常と判断します。

exec probeの設定項目 (`readinessProbe.exec`)

- **command**: 実行するコマンドを設定します (必須項目)。
- **user**: コマンドの実行ユーザーです。指定がない場合はmackerel-container-agentの実行ユーザーとなります。
- **env**: コマンドに渡す環境変数です。

設定例
```
readinessProbe:
  exec:
    command: cat /tmp/healthy
```
```
readinessProbe:
  exec:
    command: cat /tmp/healthy
    user: "sample-user"
    env:
      FOO: "FOO BAR"
  initialDelaySeconds: 10
  timeoutSeconds: 5
  periodSeconds: 3
```

#### http probe
HTTPリクエストを送信し、200番台または300番台のステータスのときに正常と判断します。

http probeの設定項目 (`readinessProbe.http`)

- **path**: リクエスト先のパスを指定します (必須項目)。
- **scheme**: URI schemeを指定します。デフォルト値は`http`です。
- **method**: リクエストのメソッドを指定します。デフォルト値は`GET`です。
- **host**: リクエスト先のホストを指定します。デフォルト値は`localhost`です。
- **port**: リクエスト先のポートを指定します。デフォルト値は`80`です。
- **headers**: リクエストヘッダを指定します。
- **proxy**: HTTPプロキシのURLを指定します。

設定例
```
readinessProbe:
  http:
    path: /healthy
    port: 8000
```
```
readinessProbe:
  http:
    scheme: http
    method: PUT
    path: /healthy
    headers:
      - name: X-Custom-Header
        value: test
    proxy: "https://proxy.example.com:8080"
  initialDelaySeconds: 10
  timeoutSeconds: 5
  periodSeconds: 10
```

#### tcp probe
TCPの接続ができれば正常と判断します。

tcp probeの設定項目 (`readinessProbe.tcp`)

- **port**: リクエスト先のポートを指定します (必須項目)。
- **host**: リクエスト先のホストを指定します。

設定例
```
readinessProbe:
  tcp:
    port: 53
```
```
readinessProbe:
  tcp:
    port: 443
    host: example.com
  initialDelaySeconds: 10
  timeoutSeconds: 5
  periodSeconds: 10
```

## 監視

コンテナを監視するためには、以下のメトリック監視を用います。監視する必要がないコンテナによってアラートが発報されてしまう場合は、環境変数 `MACKEREL_IGNORE_CONTAINER` または設定ファイルの `ignoreContainer` で除外設定を行ってください。

- CPU: `Container CPU %`
  - コンテナごとのCPU使用率 (`container.cpu.<container_name>.usage / container.cpu.<container_name>.limit`) の**最大**を監視します。
- Memory: `Container Memory %`
  - コンテナごとのMemory使用率 (`container.memory.<container_name>.usage / container.memory.<container_name>.limit`) の**最大**を監視します。
- interface: `interface.<container_name>.rxBytes.delta`, `interface.<container_name>.txBytes.delta`
  - ネットワークインターフェースの転送量を監視します。
