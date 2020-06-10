---
Title: AWS Fargate, Amazon ECS(awsvpcネットワークモード)にmackerel-container-agentをセットアップする
Date: 2018-09-21T14:10:12+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsawsvpc
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132636162484
---

AWS Fargate, Amazon ECS(awsvpcネットワークモード)におけるmackerel-container-agentのセットアップ手順です。

ECSで**awsvpcネットワークモード以外**をお使いの場合は[こちら](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecs)をご覧ください。

## 注意

mackerel-container-agent v0.1.0以降では、こちらの手順は非推奨となります。
[Amazon ECSにmackerel-container-agentをセットアップする](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecs)をご覧ください。

## 課金に関する注意事項

1つのタスクに対して1マイクロホストがMackerelに登録されます。有料プランの場合は課金が発生しますのでご注意ください。詳しくは[FAQ・ホスト数の計算方法について](https://support.mackerel.io/hc/ja/articles/360039702912-%E3%83%9B%E3%82%B9%E3%83%88%E6%95%B0%E3%81%AE%E8%A8%88%E7%AE%97%E6%96%B9%E6%B3%95%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)をご覧ください

## タスク定義にコンテナを追加する

監視したいタスク定義にmackerel-container-agentコンテナを追加します。
タスク定義の「コンテナの追加」から、下記の設定でmackerel-container-agentを追加してください。

| 項目 | 値 |
| :-- | :-- |
| コンテナ名| mackerel-container-agent |
| イメージ|  mackerel/mackerel-container-agent:latest |
| メモリ制限|  ハード制限: 128 |
| 環境変数(キー: 値) | MACKEREL_CONTAINER_PLATFORM: "ecs_awsvpc" (ECS(awsvpcネットワークモード)の場合) もしくは "fargate" (Fargateの場合)<br>MACKEREL_APIKEY: Mackerel APIキー |

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
            Value: fargate
          - Name: MACKEREL_APIKEY
            Value: <YOUR_MACKEREL_APIKEY>
        Essential: false
```

## 監視を開始する

mackerel-container-agentを追加したタスクを実行して監視を開始します。

動作しない場合はタスクのログを参照してください。
