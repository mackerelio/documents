---
Title: Amazon EKS on AWS Fargateにmackerel-container-agentをセットアップする（ベータ版機能）
Date: 2020-02-07T15:05:18+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/container/eks-on-fargate
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613508163317
CustomPath: howto/install-agent/container/eks-on-fargate
---

Amazon EKS on AWS Faragte（以降はEKS on Fargate）におけるmackerel-container-agentのセットアップ手順です。

## 注意事項

mackerel-container-agentのEKS on Fargateにおける動作はベータ版機能となります。
正式リリース時には動作条件や取得データが変更される可能性があることを、あらかじめご了承ください。

## 課金に関する注意事項

1つのPodに対して1マイクロホストがMackerelに登録されます。
有料プランの場合は課金が発生しますのでご注意ください。
詳しくは[FAQ・ホスト数の計算方法について](https://mackerel.io/ja/docs/entry/faq/contracts/calculate-host-number)をご覧ください。

## 動作条件

EKS on Fargateではmackerel-container-agent v0.3.0以降を利用する必要があります。
imageのタグを指定する場合はご注意ください。

また、後述する環境変数やKubernetesのRBACを設定する必要となります。

## Pod Templateにコンテナを追加する

監視したいPod Templateにmackerel-container-agentコンテナを指定します。

| 項目                    | 値                                                                      |
| :---------------------- | :---------------------------------------------------------------------- |
| name                    | mackerel-container-agent                                                |
| image                   | mackerel/mackerel-container-agent:latest (v0.3.0以降を指定してください) |
| imagePullPolicy         | Always                                                                  |
| resources.limits.memory | 128Mi                                                                   |
| env                     | 下記「環境変数の設定」参照                                              |

### 環境変数の設定

mackerel-container-agentでは環境変数の定義が必須となります。

- `MACKEREL_CONTAINER_PLATFORM`: "eks_fargate" (固定文字列)
- `MACKEREL_APIKEY`: Mackerel APIキー
- `MACKEREL_KUBERNETES_NODE_NAME`:

```yaml
valueFrom:
  fieldRef:
    fieldPath: spec.nodeName
```

- `MACKEREL_KUBERNETES_NAMESPACE`:

```yaml
valueFrom:
  fieldRef:
    fieldPath: metadata.namespace
```

- `MACKEREL_KUBERNETES_POD_NAME`:

```yaml
valueFrom:
  fieldRef:
    fieldPath: metadata.name
```

### RBACの設定

EKS on Fargateにおいて、mackerel-container-agentでは下記のKubernetesリソースを参照します。

| resource    | verb |
| ----------- | ---- |
| nodes/proxy | get  |
| nodes/stats | get  |
| nodes/spec  | get  |

mackerel-container-agentがこれらのリソースを参照できるよう、RBACを設定する必要があります。

以下はRBACのマニフェスト例です。

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: mackerel-container-agent
rules:
  - apiGroups: [""]
    resources:
      - nodes/proxy
      - nodes/spec
      - nodes/stats
    verbs:
      - get

---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: mackerel-container-agent-sa
  namespace: default

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: mackerel-container-agent
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: mackerel-container-agent
subjects:
  - kind: ServiceAccount
    name: mackerel-container-agent-sa
    namespace: default
```

### マニフェスト例

以下はredisコンテナをmackerel-container-agentで監視するDeploymentのマニフェスト例です。
ServiceAccountは前述のRBACのマニフェスト例で作成した "mackerel-container-agent-sa" を指定しています。

```yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: mackerel-container-agent
  namespace: default
data:
  mackerel-container-agent.yaml: |
    plugin:
      metrics:
        redis:
          command: mackerel-plugin-redis -port=6379 -timeout=5 -metric-key-prefix=redis6379

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eks-fargate-mackerel-container-agent
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  strategy: {}
  template:
    metadata:
      labels:
        app: redis
    spec:
      serviceAccountName: mackerel-container-agent-sa
      containers:
        - name: redis
          image: "redis:latest"
          imagePullPolicy: Always
          resources:
            limits:
              memory: 64Mi
              cpu: 100m
        - name: mackerel-container-agent
          image: "mackerel/mackerel-container-agent:v0.3.0-plugins"
          imagePullPolicy: Always
          volumeMounts:
            - name: mackerel-container-agent
              mountPath: "/etc/mackerel"
          env:
            - name: MACKEREL_APIKEY
              valueFrom:
                secretKeyRef:
                  name: mackerel-container-agent
                  key: apikey
            - name: MACKEREL_CONTAINER_PLATFORM
              value: "eks_fargate"
            - name: MACKEREL_KUBERNETES_NODE_NAME
              valueFrom:
                fieldRef:
                  fieldPath: spec.nodeName
            - name: MACKEREL_KUBERNETES_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
            - name: MACKEREL_KUBERNETES_POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: MACKEREL_ROLES
              value: "eks-fargate:mackerel-container-agent"
            - name: MACKEREL_AGENT_CONFIG
              value: "/etc/mackerel/mackerel-container-agent.yaml"
          resources:
            limits:
              memory: 64Mi
              cpu: 100m
      volumes:
        - name: mackerel-container-agent
          configMap:
            name: mackerel-container-agent
```

ロールやプラグインの設定は[こちら](https://mackerel.io/ja/docs/entry/howto/container-agent)の「エージェントの設定」を参照してください。

## 監視を開始する

mackerel-container-agentを追加したマニフェストをデプロイしてPodの監視を開始します。

動作しない場合はPodのログを参照してください。
