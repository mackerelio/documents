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

- Amazon EC2 Container Service(ECS)
  - **Windowsコンテナは対象外となります**
- AWS Fargate(Fargate)
- Kubernetes

## セットアップ

mackerel-container-agentのセットアップ手順です。
各コンテナオーケストレーションプラットフォームごとに手順が異なります。

- [ECS(default, bridge, host, noneネットワークモード)](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecs)
- [Fargate, ECS(awsvpcネットワークモード)](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsawsvpc)
- [Kubernetes](https://mackerel.io/ja/docs/entry/howto/install-agent/container/kubernetes)

## 取得メトリック

mackerel-container-agentで取得するメトリックはつぎのとおりです。
コンテナオーケストレーションプラットフォームによって取得内容が異なる場合があります。

| メトリック | 説明 |
| :-- | :-- |
| CPU Usage | コンテナごとのCPU使用量(1コア=100%) |
| CPU Limit | コンテナごとのCPU Limit(1コア=100%)。<br>Kubernetesではコンテナごとの `resources.limits.cpu` の値。定義がない場合はホストのコア数。<br>ECS, FargateではタスクCPU。定義がない場合はホストのCPUコア数。 |
| Memory Usage | コンテナごとのメモリ使用量 |
| Memory Limit | コンテナごとのMemory Limit。<br>Kubernetesではコンテナごとの `resources.limits.memory` の値。定義がない場合はホストの搭載メモリ量。<br>ECS, Fargateではコンテナのメモリ制限。定義がない場合はタスクメモリ。コンテナ、メモリともに定義がない場合はホストの搭載メモリ量。 |
| Interface Rx | タスク/Podのインターフェースごとの受信バイト数。<br>ECS(default, bridgeネットワークモード)ではコンテナごとのインターフェースの受信バイト数。 |
| Interface Tx | タスク/Podのインターフェースごとの送信バイト数。<br>ECS(default, bridgeネットワークモード)ではコンテナごとのインターフェースの送信バイト数。|

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
| MACKEREL_ROLES | タスク、Podのサービス、ロールを設定できます。 |
| MACKEREL_AGENT_CONFIG | エージェントの設定ファイルを設定できます。こちらの詳細は後述します。 |
| MACKEREL_IGNORE_CONTAINER | 監視を除外するコンテナの名前を正規表現で設定します。 |
| MACKEREL_HOST_STATUS_ON_START | 設定すると、エージェント起動時にホストのステータスを指定した値に変更します。 有効な値は "standby", "working", "maintenance", "poweroff" のいずれかです。|

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

#### 設定項目

| 項目名 | 説明 |
| :-- | :-- |
| apikey | エージェントがMackerelサービスとの通信の際に用いる API キーを指定します。 |
| apibase | Mackerel APIのエンドポイントです (デフォルト: `https://api.mackerelio.com/`)。 |
| roles | タスク、Podのサービス、ロールを設定できます。 |
| ignoreContainer | 監視を除外するコンテナの名前を正規表現で設定します。 |
| root | mackerel-container-agentのルートディレクトリを指定できます (デフォルト: `/var/tmp/mackerel-container-agent`)。 |
| plugin.metrics | 任意のメトリックの取得、投稿するプラグインを設定できます。こちらの詳細は後述します。 |
| plugin.checks | 任意のチェック監視を実行、その結果を投稿するプラグインを設定できます。こちらの詳細は後述します。 |
| readinessProbe | Readiness Probeを設定できます。こちらの詳細は後述します。 |

### 設定の優先順位

環境変数、設定ファイルで同じ項目が定義されていた場合、***設定ファイルの値を優先*** します。ご注意ください。

### プラグインの利用

メトリックプラグイン、チェックプラグインが利用可能です。

***公式で提供するmackerel-container-agentのDockerイメージではプラグインは同梱していません。プラグインを利用する場合は、提供するDockerイメージをベースイメージとし、利用したいプラグインをインストールしたDockerイメージを準備してください。***

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
  - コンテナごとのCPU使用率 (`container.cpu.<name>.usage / container.cpu.<name>.limit`) の**最大**を監視します。
- Memory: `Container Memory %`
  - コンテナごとのMemory使用率 (`container.memory.<name>.usage / container.memory.<name>.limit`) の**最大**を監視します。
- interface: `interface.<name>.rxBytes`, `interface.<name>.txBytes`
  - ネットワークインターフェースの転送量を監視します。
