---
Title: ECSにmackerel-container-agentをセットアップする
Date: 2019-05-16T16:07:37+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsv3
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17680117127134474239
CustomPath: howto/install-agent/container/ecsv3
---

ECSにおけるmackerel-container-agentのセットアップ手順です。

## 課金に関する注意事項

1つのタスクに対して1つのホストがMackerelに登録されます。有料プランの場合は課金が発生しますのでご注意ください。詳しくは[FAQ・ホスト数の計算方法について](https://mackerel.io/ja/docs/entry/faq/contracts/calculate-host-number)をご覧ください

## タスク定義にコンテナを追加する

監視したいタスク定義にmackerel-container-agentコンテナを追加します。
タスク定義の「コンテナの追加」から、下記の設定でmackerel-container-agentを追加してください。

| 項目 | 値 |
| :-- | :-- |
| コンテナ名| mackerel-container-agent |
| イメージ|  mackerel/mackerel-container-agent:latest |
| メモリ制限|  ハード制限: 128 |
| 環境変数(キー: 値) | MACKEREL_CONTAINER_PLATFORM: "ecs_v3"<br>MACKEREL_APIKEY: Mackerel APIキー |

ロールやプラグインを利用する場合は[こちら](https://mackerel.io/ja/docs/entry/howto/container-agent)の「エージェント設定」も参照してください。

### CloudFormationテンプレート例(YAML)

```
TaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    RequiresCompatibilities:
      - FARGATE
    NetworkMode: awsvpc
    Memory: 512
    Cpu: 256
    ContainerDefinitions:
      - Name: alpine
        Image: alpine:latest
        Memory: 64
        EntryPoint:
          - sh
          - -c
        Command:
          - "/bin/sh -c \"while true; do sleep 1; done\""
        Essential: true
      - Name: mackerel-container-agent
        Image: mackerel/mackerel-container-agent:latest
        Memory: 128
        Environment:
          - Name: MACKEREL_CONTAINER_PLATFORM
            Value: ecs_v3
          - Name: MACKEREL_APIKEY
            Value: <YOUR_MACKEREL_APIKEY>
        Essential: false
```

## 監視を開始する

mackerel-container-agentを追加したタスクを実行して監視を開始します。

動作しない場合はタスクのログを参照してください。

## 制限事項

EC2起動タイプのbridgeネットワークモードの場合、ネットワークインターフェースメタデータはmackerel-container-agentコンテナのもののみ取得します。
タスクに含まれる他のコンテナのネットワークインターフェースメタデータは取得しないのでご注意ください。
