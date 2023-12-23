# Kubernetes

## docs

- [Initコンテナ | Kubernetes](https://kubernetes.io/ja/docs/concepts/workloads/pods/init-containers/)
- [TaintとToleration | Kubernetes](https://kubernetes.io/ja/docs/concepts/scheduling-eviction/taint-and-toleration/)
- 設定関連
    - [Set Kubelet Parameters Via A Configuration File | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/)
    - [kube-controller-manager | Kubernetes](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)

## pod

### CrashLoopBackOff

`describe`または`event`で

```console
Failed to pull image "quay.io/ansible/awx-operator:0.16.1-12-g768bc2f": rpc error: code = NotFound desc = failed to pull and unpack image "quay.io/ansible/awx-operator:0.16.1-12-g768bc2f": failed to resolve reference "quay.io/ansible/awx-operator:0.16.1-12-g768bc2f": quay.io/ansible/awx-operator:0.16.1-12-g768bc2f: not found
```

や、

```console
Error: ImagePullBackOff
```

の場合は、イメージがリポジトリに無い。指定が合ってるか確認。  
podのステータスも`ErrImagePull`になる。

---

`logs`で

```console
standard_init_linux.go:228: exec user process caused: exec format error
```

の場合、イメージのアーキテクチャと実行環境のアーキテクチャが合ってない。  
イメージはamd64だけど実行環境がarm64など。

## service

### FQCN (A/AAAAレコード)

podからは`<service-name>.<namespace-name>.svc.cluster.local`で名前解決できる。
