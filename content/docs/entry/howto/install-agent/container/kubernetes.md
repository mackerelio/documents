---
Title: Setting up mackerel-container-agent in Kubernetes
Date: 2019-02-07T17:47:30+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/container/kubernetes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/98012380860620872
---

This document details the setup process of mackerel-container-agent for Kubernetes.

## Point to note regarding billing

For every one pod, a micro host will be registered in Mackerel. Please note that charges will occur if using a paid plan. For more details, refer to [FAQ · Calculating the number of hosts](https://support.mackerel.io/hc/en-us/articles/360039702912).

## Operating conditions

By default, mackerel-container-agent accesses the kubelet read-only-port.

In environments where read-only-port is disabled (`--read-only-port=0`), the environment variable and Role configurations described below are required.

## Adding the container to the Pod Template

Specify the mackerel-container-agent container in the Pod Template that you would like to monitor.


| Item | Value |
| :-- | :-- |
| name | mackerel-container-agent |
| image |  mackerel/mackerel-container-agent:latest |
| imagePullPolicy| Always |
| resources.limits.memory | 128Mi |
| env | See below |

### Environment variable configuration

Definitions for environment variables are required with mackerel-container-agent.

- `MACKEREL_CONTAINER_PLATFORM`: kubernetes
- `MACKEREL_APIKEY`: Mackerel API key
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

The following items are configured arbitrarily.

- `MACKEREL_KUBERNETES_KUBELET_READ_ONLY_PORT`: Port number
  - When changing the port number by specifying the --read-only-port flag in kubelet (Default: 10255)
  - Setting 0 will stop the use of the kubelet read-only port
- `MACKEREL_KUBERNETES_KUBELET_PORT`: Port number
  - When changing the port number by specifying the --port flag in kubelet (Default: 10250)
  - When accessing the kubelet API with `/var/run/secrets/kubernetes.io/serviceaccount/ca.crt` as the CA certificate, the content of `/var/run/secrets/kubernetes.io/serviceaccount/token` is used as the Bearer token. Please note that this field does not exist when automountServiceAccountToken is configured as false (true by default).
- `MACKEREL_KUBERNETES_KUBELET_INSECURE_TLS`: Arbitrary value
  - Certificates are not verified when accessing the kubelet API in `MACKEREL_KUBERNETES_KUBELET_PORT`

### Manifest example 

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

When using roles or plugins, refer to the "Agent configurations" [here](https://mackerel.io/docs/entry/howto/container-agent).

### mackerel-container-api resource reference

The following resources are referred to in mackerel-container-agent.

| resource    | verb |
| ----------- | ---- |
| nodes/proxy | get  |
| nodes/stats | get  |

If authentication is enabled for kubelet, Role configuration for ServiceAccount will be necessary so that mackerel-container-agent can refer to these resources.

For more on kubelet authentication, see the [Kubernetes document](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-authentication-authorization/).

The following is a RBAC configuration example.

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

## Start monitoring

Deploy the mackerel-container-agent added manifest and start Pod monitoring.

If this does not work, please refer to the Pod log.
