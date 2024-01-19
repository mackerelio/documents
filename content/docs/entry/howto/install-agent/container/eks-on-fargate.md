---
Title: Setting up mackerel-container-agent for Amazon EKS on AWS Fargate (beta version)
Date: 2020-02-12T11:08:21+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/container/eks-on-fargate
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613510289856
---

This document details the setup process for mackerel-container-agent on Amazon EKS on AWS Faragte（hereinafter EKS on Fargate).

## Point to note

Please note that the operation of mackerel-container-agent for EKS on Fargate is still a beta feature.
Some operating conditions and retrieved data may change upon its official release.

## Regarding billing

For every one pod, one micro host will be registered in Mackerel.
Please note that charges will occur if using a paid plan.
For more details, refer to [FAQ · Calculating the number of hosts](https://support.mackerel.io/hc/en-us/articles/360039702912).

## Operating Conditions

mackerel-container-agent v0.3.0 or later is required for operation with EKS on Fargate.
Please be careful when specifying the image tag.

Furthermore, the environment variables and the Kubernetes RBAC later mentioned in this document must be configured.

## Adding the container to the Pod Template

Specify the mackerel-container-agent container in the Pod Template you want to monitor.

| Item                    | Value                                                      |
| :---------------------- | :----------------------------------------------------------|
| name                    | mackerel-container-agent                                   |
| image                   | mackerel/mackerel-container-agent:latest (v0.3.0 of later) |
| imagePullPolicy         | Always                                                     |
| resources.limits.memory | 128Mi                                                      |
| env                     | See "Environment variable configuration" below             |

### Environment variable configuration

Environment variables must be defined with mackerel-container-agent.

- `MACKEREL_CONTAINER_PLATFORM`: "eks_fargate" (fixed character string)
- `MACKEREL_APIKEY`: Mackerel API key
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

### RBAC configuration

The following Kubernetes resources are used with mackerel-container-agent for EKS on Fargate.

| resource    | verb |
| ----------- | ---- |
| nodes/proxy | get  |
| nodes/stats | get  |

RBAC needs to be configured so that mackerel-container-agent can refer to these resources.

The following is an RBAC manifest example.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: mackerel-container-agent
rules:
  - apiGroups: [""]
    resources:
      - nodes/proxy
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

### Manifest example

Below is an example of the deployment manifest for monitoring a redis container with mackerel-container-agent.
ServiceAccount specifies "mackerel-container-agent-sa" which was created in the above-mentioned RBAC manifest example.

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

Refer to the 'Agent configuration' section [here](https://mackerel.io/docs/entry/howto/container-agent) when using roles and plugins.

## Start monitoring

Deploy the mackerel-container-agent added manifest and start Pod monitoring.

If this does not work, please refer to the Pod log.
