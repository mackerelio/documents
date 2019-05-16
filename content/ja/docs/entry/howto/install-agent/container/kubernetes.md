---
Title: Kubernetesにmackerel-container-agentをセットアップする
Date: 2018-09-21T15:08:13+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/container/kubernetes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132636177807
---

Kubernetesにおけるmackerel-container-agentのセットアップ手順です。

## 課金に関する注意事項

1つのPodに対して1マイクロホストがMackerelに登録されます。有料プランの場合は課金が発生しますのでご注意ください。詳しくは[FAQ・ホスト数の計算方法について](https://mackerel.io/ja/docs/entry/faq/contracts/calculate-host-number)をご覧ください

## 動作条件

mackerel-container-agentはデフォルトでkubeletのread-only-portにアクセスします。

read-only-portが無効な環境(`--read-only-port=0`)では後述する環境変数やRoleの設定が必要となります。

## Pod Templateにコンテナを追加する

監視したいPod Templateにmackerel-container-agentコンテナを指定します。


| 項目 | 値 |
| :-- | :-- |
| name | mackerel-container-agent |
| image |  mackerel/mackerel-container-agent:latest |
| imagePullPolicy| Always |
| resources.limits.memory | 128Mi |
| env | 下記参照 |

### 環境変数の設定

mackerel-container-agentでは環境変数の定義が必須となります。

- `MACKEREL_CONTAINER_PLATFORM`: kubernetes
- `MACKEREL_APIKEY`: Mackerel APIキー
- `MACKEREL_KUBERNETES_KUBELET_HOST`:
```
valueFrom:
  fieldRef:
    fieldPath: status.hostIP
```
- `MACKEREL_KUBERNETES_NAMESPACE`:
```
valueFrom:
  fieldRef:
    fieldPath: metadata.namespace
```
- `MACKEREL_KUBERNETES_POD_NAME`:
```
valueFrom:
  fieldRef:
    fieldPath: metadata.name
```

以下の項目は任意で設定します。

- `MACKEREL_KUBERNETES_KUBELET_READ_ONLY_PORT`: ポート番号
  - kubeletで--read-only-portフラグを指定してポート番号を変更している場合(デフォルト: 10255)
  - 0 を設定するとkubeletのread-only portの利用を中止します
- `MACKEREL_KUBERNETES_KUBELET_PORT`: ポート番号
  - kubeletで--portフラグを指定してポート番号を変更している場合(デフォルト: 10250)
  - kubelet APIアクセスの際にCA証明書として `/var/run/secrets/kubernetes.io/serviceaccount/ca.crt` を、Bearerトークンとして `/var/run/secrets/kubernetes.io/serviceaccount/token` の内容を使用します。automountServiceAccountTokenをfalseに設定している場合はファイルが存在しないので注意してください(デフォルトはtrue)。
- `MACKEREL_KUBERNETES_KUBELET_INSECURE_TLS`: 任意の値
  - `MACKEREL_KUBERNETES_KUBELET_PORT` へのkubelet APIアクセスの際に証明書の検証を行いません

### マニフェスト例

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  containers:
  # ...
  - name: mackerel-container-agent
    image: mackerel/mackerel-container-agent:latest
    imagePullPolicy: Always
    resources:
      limits:
        memory: 128Mi
    env:
      - name: MACKEREL_CONTAINER_PLATFORM
        value: kubernetes
      - name: MACKEREL_APIKEY
        value: <Mackerel APIKey>
      - name: MACKEREL_KUBERNETES_KUBELET_HOST
        valueFrom:
          fieldRef:
            fieldPath: status.hostIP
      - name: MACKEREL_KUBERNETES_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MACKEREL_KUBERNETES_POD_NAME
        valueFrom:
         fieldRef:
            fieldPath: metadata.name
```

ロールやプラグインを利用する場合は[こちら](https://mackerel.io/ja/docs/entry/howto/container-agent)の「エージェント設定」も参照してください。

### mackerel-container-apiが参照するリソース

mackerel-container-agentでは下記のリソースに参照します。

| resource    | verb |
| ----------- | ---- |
| nodes/proxy | get  |
| nodes/stats | get  |
| nodes/spec  | get  |

kubeletで認証が有効な場合には、mackerel-container-agentがこれらのリソースを参照できるよう、ServiceAccountに対してRoleを設定する必要となります。

kubeletの認証については[Kubernetesのドキュメント](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-authentication-authorization/)を御覧ください。

以下はRBACの設定例です。

```
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: mackerel-container-agent-clusterrole
rules:
- apiGroups:
  - ""
  resources:
  - nodes/proxy
  - nodes/stats
  - nodes/spec
  verbs:
  - get
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: sample-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: mackerel-container-agent-clusterrole
subjects:
- kind: ServiceAccount
  name: sample-serviceaccount
  namespace: default
```

## 監視を開始する

mackerel-container-agentを追加したマニフェストをデプロイしてPodの監視を開始します。

動作しない場合はPodのログを参照してください。
