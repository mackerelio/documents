---
Title: Amazon ECS(default, bridge, host, noneネットワークモード)にmackerel-container-agentをセットアップする
Date: 2019-06-17T11:33:18+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsbasic
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17680117127201755862
---

Amazon ECS(default, bridge, host, noneネットワークモード)におけるmackerel-container-agentのセットアップ手順です。

## 注意

mackerel-container-agent v0.1.0以降では、こちらの手順は非推奨となります。
[Amazon ECSにmackerel-container-agentをセットアップする](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecs)をご覧ください。

## 課金に関する注意事項

1つのタスクに対して1マイクロホストがMackerelに登録されます。有料プランの場合は課金が発生しますのでご注意ください。詳しくは[FAQ・ホスト数の計算方法について](https://support.mackerel.io/hc/ja/articles/360039702912-%E3%83%9B%E3%82%B9%E3%83%88%E6%95%B0%E3%81%AE%E8%A8%88%E7%AE%97%E6%96%B9%E6%B3%95%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)をご覧ください

## サポートするネットワークモード

こちらの手順ではつぎのECSのネットワークモードが対象となります。

- default
- bridge
- host
- none

**awsvpcネットワークモード**を利用する場合は[Fargate, ECS(awsvpcネットワークモード)にmackerel-container-agentをセットアップする](https://mackerel.io/ja/docs/entry/howto/install-agent/container/ecsawsvpc)の手順を参考にしてください。

## 動作条件

mackerel-container-agentコンテナ内のユーザーはroot(uid=0)である必要があります。
コンテナ内のユーザーを変更している場合ご注意ください。

## タスク定義にコンテナを追加する

監視したいタスク定義にmackerel-container-agentコンテナを追加します。

### ボリュームの追加

mackerel-container-agentで利用するボリュームを追加します。
タスク定義の「ボリュームの追加」から、つぎのボリュームを追加してください。

| 名前 | ソースパス |
| :-- | :-- |
| cgroup | /cgroup (AL2の場合: /sys/fs/cgroup) |
| docker_sock | /var/run/docker.sock |

## コンテナの追加

タスク定義の「コンテナの追加」から、下記の設定でmackerel-container-agentを追加してください。

| 項目 | 値 |
| :-- | :-- |
| コンテナ名| mackerel-container-agent |
| イメージ|  mackerel/mackerel-container-agent:latest |
| メモリ制限|  ハード制限: 128 |
| マウントポイント<br>(ソースボリューム: コンテナパス)| cgroup: /host/sys/fs/cgroup<br>docker_sock: /var/run/docker.sock<br>どちらも読み取り専用にチェックしてください |
| 環境変数(キー: 値) | MACKEREL_CONTAINER_PLATFORM: ecs<br>MACKEREL_APIKEY: Mackerel APIキー |

ロールやプラグインを利用する場合は[こちら](https://mackerel.io/ja/docs/entry/howto/container-agent)の「エージェント設定」も参照してください。

### CloudFormationテンプレート例(YAML)

```
TaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Volumes:
      - Name: cgroup
        Host:
          SourcePath: /cgroup
      - Name: docker_sock
        Host:
          SourcePath: /var/run/docker.sock
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
        MountPoints:
          - SourceVolume: cgroup
            ContainerPath: /host/sys/fs/cgroup
            ReadOnly: true
          - SourceVolume: docker_sock
            ContainerPath: /var/run/docker.sock
            ReadOnly: true
        Environment:
          - Name: MACKEREL_CONTAINER_PLATFORM
            Value: ecs
          - Name: MACKEREL_APIKEY
            Value: <YOUR_MACKEREL_APIKEY>
        Essential: false
```

## 監視を開始する

mackerel-container-agentを追加したタスクを実行して監視を開始します。

動作しない場合はタスクのログを参照してください。
