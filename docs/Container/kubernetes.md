# Kubernetes

## docs

- [Initコンテナ | Kubernetes](https://kubernetes.io/ja/docs/concepts/workloads/pods/init-containers/)
- [TaintとToleration | Kubernetes](https://kubernetes.io/ja/docs/concepts/scheduling-eviction/taint-and-toleration/)
- 設定関連
    - [Set Kubelet Parameters Via A Configuration File | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/)
    - [kube-controller-manager | Kubernetes](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)
- [永続ボリューム | Kubernetes](https://kubernetes.io/ja/docs/concepts/storage/persistent-volumes/)

## ヘルスチェック

[Kubernetes API health endpoints | Kubernetes](https://kubernetes.io/docs/reference/using-api/health-checks/)

APIエンドポイントとして `/readyz` と `/livez` が用意されている。

```console
$ curl -k 'https://192.168.0.131:6443/readyz'
ok
```

K3sだと認証が必要

```console
$ curl -k -H "Authorization: Bearer $TOKEN" 'https://192.168.0.75:6443/livez?verbose'
[+]ping ok
[+]log ok
[+]etcd ok
[+]poststarthook/start-kube-apiserver-admission-initializer ok
[+]poststarthook/generic-apiserver-start-informers ok
[+]poststarthook/priority-and-fairness-config-consumer ok
[+]poststarthook/priority-and-fairness-filter ok
[+]poststarthook/storage-object-count-tracker-hook ok
[+]poststarthook/start-apiextensions-informers ok
[+]poststarthook/start-apiextensions-controllers ok
[+]poststarthook/crd-informer-synced ok
[+]poststarthook/start-service-ip-repair-controllers ok
[+]poststarthook/rbac/bootstrap-roles ok
[+]poststarthook/scheduling/bootstrap-system-priority-classes ok
[+]poststarthook/priority-and-fairness-config-producer ok
[+]poststarthook/start-system-namespaces-controller ok
[+]poststarthook/bootstrap-controller ok
[+]poststarthook/start-cluster-authentication-info-controller ok
[+]poststarthook/start-kube-apiserver-identity-lease-controller ok
[+]poststarthook/start-deprecated-kube-apiserver-identity-lease-garbage-collector ok
[+]poststarthook/start-kube-apiserver-identity-lease-garbage-collector ok
[+]poststarthook/start-legacy-token-tracking-controller ok
[+]poststarthook/aggregator-reload-proxy-client-cert ok
[+]poststarthook/start-kube-aggregator-informers ok
[+]poststarthook/apiservice-registration-controller ok
[+]poststarthook/apiservice-status-available-controller ok
[+]poststarthook/kube-apiserver-autoregistration ok
[+]autoregister-completion ok
[+]poststarthook/apiservice-openapi-controller ok
[+]poststarthook/apiservice-openapiv3-controller ok
[+]poststarthook/apiservice-discovery-controller ok
livez check passed
```

tokenはsecretリソースを作成して取得する。  
[[Kubernetes] ヘルスチェックAPIを試す (bearer token認証付き) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2024/02/05/101259)

## k8sリソース

### pod

#### CrashLoopBackOff

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

### cronjob

#### Job/Podの自動削除

CronJobリソースの`.spec.successfulJobsHistoryLimit`と`.spec.failedJobsHistoryLimit`の値で成功と失敗それぞれの保持数が設定される。  
超過分は自動で削除される。

<https://kubernetes.io/ja/docs/tasks/job/automated-tasks-with-cron-jobs/#job-history-limit>

### service

#### FQCN (A/AAAAレコード)

podからは`<service-name>.<namespace-name>.svc.cluster.local`で名前解決できる。

## ストレージ

### PV拡張

[Resizing Persistent Volumes using Kubernetes | Kubernetes](https://kubernetes.io/blog/2018/07/12/resizing-persistent-volumes-using-kubernetes/)

## 消えないnamespace

`kubectl delete ns hoge`が応答無くなりstatusがTerminatingのままになった場合の対処。

```console
kubectl get ns hoge -o json > deletens.json
```

namespace定義の中から、`spec.finalizers["kubernetes"]`の定義を見つけ、中身を空にする。

```json
    "spec": {
        "finalizers": []
    },
```

最後に編集したファイルをパラメタに、以下コマンドを実行

```console
kubectl replace --raw "/api/v1/namespaces/hoge/finalize" -f deletens.json 
```

これで削除できるはず。

namespaceの定義が簡単な場合は以下コマンドでも行ける。

```console
kubectl get ns ${namespace} -o json | grep -v -- '"kubernetes"' | kubectl replace --raw "/api/v1/namespaces/${namespace}/finalize" -f -
```

※ `kubectl get ns hoge -o json | grep -2 -- '"kubernetes"'`を実行して、1か所しかヒットしない場合

## Initコンテナ

- [Initコンテナ | Kubernetes](https://kubernetes.io/ja/docs/concepts/workloads/pods/init-containers/)
- [Initコンテナのデバッグ | Kubernetes](https://kubernetes.io/ja/docs/tasks/debug/debug-application/debug-init-containers/)

## RBAC

[Authorization | Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)

### verbs

| request          | HTTP       |       |
| ---------------- | ---------- | ----- |
| create           | POST       |       |
| get              | GET / HEAD |       |
| list             | GET / HEAD | 一覧の参照 |
| watch            | GET / HEAD | 変更の監視 |
| update           | PUT        |       |
| patch            | PATCH      |       |
| delete           | DELETE     |       |
| deletecollection | DELETE     |       |

[get vs. list in Kubernetes RBAC - Stack Overflow](https://stackoverflow.com/questions/58159866/get-vs-list-in-kubernetes-rbac)

リソース単位で操作に必要なverbsは`kubectl api-resources -o wide`で確認できる。

## crictl

### pod一覧

```console
crictl pods
```

```console
crictl pods --namespace <NAMESPACE> --name <POD_NAME>
```

### コンテナ一覧

```console
crictl ps
```

```console
crictl ps --name <CONTAINER_NAME>
```

`--name`は正規表現可能

```console
crictl ps --name 'awx-.*'
```

### コンテナ毎のリソース消費状況

```console
crictl stats
```

## REST

### namespace一覧

```console
curl https://k8s.example.org:6443/api/v1/namespaces
```

`NamespaceList`が返る。
