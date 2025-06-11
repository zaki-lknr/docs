# kubectl

分類変えるかも…

## オプション

### kubeconfigファイル指定

環境変数`KUBECONFIG`か、オプション`--kubeconfig`に指定する

## コンテキスト

### 一覧

```console
kubectl config get-contexts
```

### 切り替え

```console
kubectl config use-contexts <context-name>
```

### 削除

```console
zaki@cloud-dev2:~$ kubectl config get-contexts 
CURRENT   NAME                    CLUSTER                 AUTHINFO                NAMESPACE
          k3s-node                k3s-node                k3s-node                
          k3s-oci-4c              k3s-oci-4c              k3s-oci-4c              
          kind-ansible-cookbook   kind-ansible-cookbook   kind-ansible-cookbook   
          kind-awx-sample         kind-awx-sample         kind-awx-sample         
*         rasp5-01                rasp5-01                rasp5-01                
zaki@cloud-dev2:~$ kubectl config delete-context rasp5-01 
warning: this removed your active context, use "kubectl config use-context" to select a different one
deleted context rasp5-01 from /home/zaki/.kube/config
zaki@cloud-dev2:~$ kubectl config get-contexts 
CURRENT   NAME                    CLUSTER                 AUTHINFO                NAMESPACE
          k3s-node                k3s-node                k3s-node                
          k3s-oci-4c              k3s-oci-4c              k3s-oci-4c              
          kind-ansible-cookbook   kind-ansible-cookbook   kind-ansible-cookbook   
          kind-awx-sample         kind-awx-sample         kind-awx-sample         
```

current削除時は警告されるが削除は可能

## 設定

### kubeconfigのマージ

```console
$ KUBECONFIG=new-kubeconfig.yaml:$HOME/.kube/config kubectl config view --flatten > $HOME/.kube/tmp
$ mv $HOME/.kube/tmp $HOME/.kube/config
```

### タブ補完

```bash
source <(kubectl completion bash)
complete -o default -F __start_kubectl kc
```

[[oc / kubectl] コマンドや引数の補完設定 【completion】 - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/03/02/205509)

`kind`や`helm`も同じ要領。

tab押下で `_get_comp_words_by_ref: command not found` エラーが出るときは、`bash-completion`が無いのでパッケージ追加する。

```console
$ kubectl get -bash: _get_comp_words_by_ref: command not found
```

## create

### job

#### 既存CronJobから即時実行のjobを作成

CronJobリソースの定義をもとに、即時実行するJobリソースを作成する

```console
kubectl create job exec --from=cronjob/cronjob-resource -n namespace
```

### secret

#### ファイルのsecret

キー名: ファイル内容のsecret作る

```console
$ cat sample.txt 
hello
kubernetes!
$ kubectl create secret generic sample-secret --from-file=lines=sample.txt 
secret/sample-secret created
$ kubectl get secret sample-secret
NAME            TYPE     DATA   AGE
sample-secret   Opaque   1      6s
```

```yaml
$ kubectl get secret sample-secret -o yaml
apiVersion: v1
data:
  lines: aGVsbG8Ka3ViZXJuZXRlcyEK
kind: Secret
metadata:
  creationTimestamp: "2021-07-07T12:12:03Z"
  name: sample-secret
  namespace: default
  resourceVersion: "1041860"
  uid: 9675c818-cb9b-4b7f-a29c-c60bf5a908aa
type: Opaque
```

```console
$ kubectl get secret sample-secret -o jsonpath='{.data.lines}' | base64 -d
hello
kubernetes!
```

これはConfigMapと同じ

#### key=valueのsecret

```console
$ kubectl create secret generic sample-secret --from-literal=username=zaki
secret/sample-secret created
```

このコマンド実行で以下のsecretリソースが作成される。

```yaml
$ kubectl get secret sample-secret -o yaml
apiVersion: v1
data:
  username: emFraQ==
kind: Secret
metadata:
  creationTimestamp: "2022-03-24T02:57:06Z"
  name: sample-secret
  namespace: default
  resourceVersion: "298054"
  uid: 35c9b58a-247d-4243-b292-193d811f140a
type: Opaque
```

#### TLS証明書のsecret

事前に`server.crt`と`server.key`は生成しておく。

```console
kubectl create secret tls host-secret -n kube-system \
  --cert=server.crt \
  --key=server.key
```

以下のsecretリソースが作成される

```yaml
apiVersion: v1
data:
  tls.crt: ...
  tls.key: ...
kind: Secret
metadata:
  creationTimestamp: "2024-11-24T08:30:11Z"
  name: host-secret
  namespace: kube-system
  resourceVersion: "140527"
  uid: e5399e4b-7d62-4c57-b97e-a9b2d65d1375
type: kubernetes.io/tls
```

#### コンテナレジストリ認証情報のsecret

ECRアクセスの認証情報なら以下。

```console
kubectl create secret docker-registry credential \
  --docker-server=****.dkr.ecr.ap-northeast-1.amazonaws.com \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password --region ap-northeast-1)
```

#### マニフェストでsecret

マニュフェストから作成する場合は、base64エンコード済みであること。

```yaml
$ cat secret-sample.yaml 
apiVersion: v1
data:
  username: emFraQ==
kind: Secret
metadata:
  name: sample-secret2
  namespace: default
type: Opaque
```

```yaml
$ kubectl apply -f secret-sample.yaml 
secret/sample-secret2 created
$ kubectl get secret sample-secret2 -o yaml
apiVersion: v1
data:
  username: emFraQ==
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"username":"emFraQ=="},"kind":"Secret","metadata":{"annotations":{},"name":"sample-secret2","namespace":"default"},"type":"Opaque"}
  creationTimestamp: "2022-03-24T03:01:32Z"
  name: sample-secret2
  namespace: default
  resourceVersion: "298241"
  uid: a1ae0bf1-3434-4f3d-88d3-a1bb33dee6ac
type: Opaque
```

## patch

### 値の更新

```console
kubectl patch -n namespace pdb resource -p '{ "spec": { "maxUnavailable": 0 }}'
```

### キーと値の追加(辞書)

定義が存在しなかった`spec.init_container_image`キーに値を追加する。

```console
kubectl patch -n namespace awx awx-demo -p '{"spec":{"init_container_image":"quay.io/ansible/awx-ee"}}' --type merge
```

`--type merge`がないとエラーになる。

```console
$ kubectl patch -n namespace awx awx-demo -p '{"spec":{"init_container_image":"quay.io/ansible/awx-ee"}}'
error: application/strategic-merge-patch+json is not supported by awx.ansible.com/v1beta1, Kind=AWX: the body of the request was in an unknown format - accepted media types include: application/json-patch+json, application/merge-patch+json, application/apply-patch+yaml
```

## run

### ワーク用のpodをデプロイする

```console
$ kubectl run work-pod --image=fedora --command -- tail -f /dev/null
```

マニフェストにするとこんな感じ

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: work-pod
spec:
  containers:
  - name: work-pod
    image: fedora
    command:
    - tail
    - -f
    - /dev/null
```

## expose

### podベースでserviceを作成

```console
$ kubectl expose pod <pod-name> --port 80
```

## config

[[kubectl / oc]コンテキストやクラスタ情報の確認 (コマンドメモ) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/04/07/080241)

`~/.kube/config`(`$KUBECONFIG`)の内容から証明書のデータをマスクした情報

```
$ kubectl config view
```

現在のクラスタ情報

```
$ kubectl cluster-info 
Kubernetes control plane is running at https://192.168.0.121:6443
CoreDNS is running at https://192.168.0.121:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://192.168.0.121:6443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

KUBECONFIGで持っているクラスタ一覧

```
$ kubectl config get-clusters
NAME
cluster-c2daobwhbsg
kind-multi-1.15
zks
kind-kind
kind-multicluster
kubernetes
local-k3s
oci-k3s-le
oci-k3s
sample-cluster
```

コンテキスト情報

```
$ kubectl config get-contexts
CURRENT   NAME                          CLUSTER               AUTHINFO                               NAMESPACE
          context-c2daobwhbsg           cluster-c2daobwhbsg   user-c2daobwhbsg                       
          kind-kind                     kind-kind             kind-kind                              
          kind-multi-1.15               kind-multi-1.15       kind-multi-1.15                        
          kind-multicluster             kind-multicluster     kind-multicluster                      
          kubernetes-admin@kubernetes   kubernetes            kubernetes-admin                       
          kubernetes-admin@zks          zks                   kubernetes-admin                       
          local-k3s                     local-k3s             local-k3s                              
          oci-k3s                       oci-k3s               oci-k3s                                
*         oci-k3s-le                    oci-k3s-le            oci-k3s-le                             
          sample-cluster                sample-cluster        clusterUser_sample-rg_sample-cluster  
```

現在のコンテキスト

```
$ kubectl config current-context
oci-k3s-le
```

コンテキストをセットする

```console
$ kubectl config use-context k3d-k3s-default
Switched to context "k3d-k3s-default".
```

複数のkubeconfigのマージするには、`:`で繋げて`view --merge --flatten`する。

```console
KUBECONFIG=~/.kube/config:kubeconfig-1.yaml:kubeconfig-2.yaml kubectl config view --merge --flatten > tmp.yaml
mv tmp.yaml ~/.kube/config
```

コンテキスト名の変更

```console
$ kubectl config get-contexts 
CURRENT   NAME                    CLUSTER                 AUTHINFO                NAMESPACE
*         default                 default                 default                 
          kind-ansible-cookbook   kind-ansible-cookbook   kind-ansible-cookbook   
          kind-awx-sample         kind-awx-sample         kind-awx-sample
$ kubectl config rename-context default rasp5-01
Context "default" renamed to "rasp5-01".
$ kubectl config get-contexts 
CURRENT   NAME                    CLUSTER                 AUTHINFO                NAMESPACE
          kind-ansible-cookbook   kind-ansible-cookbook   kind-ansible-cookbook   
          kind-awx-sample         kind-awx-sample         kind-awx-sample         
*         rasp5-01                default                 default
```

## delete

### 指定namespaceの指定typeを全部消す

```console
$ kubectl delete pvc -n zzz --all
```

## wait

リソースが指定の条件になるまで待つ。  
podがrunningになるまで待つには以下。

```console
kubectl wait --for=jsonpath='{.status.phase}'=Running pod/*****
```

## get

### 全てのリソース

`kubectl get all`だと全リソースが対象ではないが、本当にすべてのリソースを取得するには以下

```console
kubectl get "$(kubectl api-resources --namespaced=true --verbs=list -o name | tr "\n" "," | sed -e 's/,$//')"
```

pluginを使ってサブコマンドにしておくと便利。  
※ rootで実行

```console
cat <<'EOL' > /usr/local/bin/kubectl-get_all
#!/usr/bin/env bash

set -e -o pipefail; [[ -n "$DEBUG" ]] && set -x

exec kubectl get "$(kubectl api-resources --namespaced=true --verbs=list --output=name | tr "\n" "," | sed -e 's/,$//')" "$@"
EOL
chmod +x /usr/local/bin/kubectl-get_all
```

実行

```console
kubectl get-all -n awx
```

### eventで作成時ソート

```console
kubectl get event --sort-by='.metadata.creationTimestamp'
```

### ヘルスチェック

```console
$kubectl get --raw='/readyz'
ok
```

あるいは

```console
$ kubectl get --raw='/readyz?verbose'
[+]ping ok
[+]log ok
[+]etcd ok
[+]etcd-readiness ok
[+]informer-sync ok
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
[+]shutdown ok
readyz check passed
```

### pod

#### イメージ一覧

```console
kubectl get pod -n awx -o jsonpath="{..image}" | sed 's/ /\n/g' | sort | uniq
```

これでもいいかな？

```console
kubectl get pod -n awx -o yaml | grep image: | sort | uniq
```

podに紐づけるには

```console
$ kubectl get pod -n awx -o custom-columns=NAMESPACE:.metadata.namespace,NAME:.metadata.name,IMAGE:.spec.containers[*].image
NAMESPACE   NAME                                               IMAGE
awx         awx-migration-24.6.1-dh8l8                         quay.io/ansible/awx:24.6.1
awx         awx-operator-controller-manager-745b55d94b-trdrs   gcr.io/kubebuilder/kube-rbac-proxy:v0.15.0,quay.io/ansible/awx-operator:2.19.1
awx         awx-postgres-15-0                                  quay.io/sclorg/postgresql-15-c9s:latest
awx         awx-task-dc97f74f7-fmbbn                           docker.io/redis:7,quay.io/ansible/awx:24.6.1,quay.io/ansible/awx-ee:24.6.1,quay.io/ansible/awx:24.6.1
awx         awx-web-769f8f749f-s4qkz                           docker.io/redis:7,quay.io/ansible/awx:24.6.1,quay.io/ansible/awx:24.6.1
```

#### podとコンテナ名

```console
zaki@cloud-dev2:~$ kubectl get pod -n awx -o custom-columns=NAMESPACE:.metadata.namespace,NAME:.metadata.name,IMAGE:.spec.containers[*].name
NAMESPACE   NAME                                               IMAGE
awx         awx-migration-24.6.1-dh8l8                         migration-job
awx         awx-operator-controller-manager-745b55d94b-trdrs   kube-rbac-proxy,awx-manager
awx         awx-postgres-15-0                                  postgres
awx         awx-task-dc97f74f7-fmbbn                           redis,awx-task,awx-ee,awx-rsyslog
awx         awx-web-769f8f749f-s4qkz                           redis,awx-web,awx-rsyslog
```

#### podとIPアドレス (custom-columns)

```console
$ kubectl get pod -o custom-columns=NAME:.metadata.name,IP:.status.podIP --no-headers
NAME                           IP
demo-server-67dcd86d64-7hfck   10.42.0.14
demo-server-67dcd86d64-82h8j   10.42.0.9
demo-server-67dcd86d64-frcs6   10.42.0.11
demo-server-67dcd86d64-fvl9p   10.42.0.15
demo-server-67dcd86d64-hf6l2   10.42.0.13
demo-server-67dcd86d64-jv7sq   10.42.0.12
demo-server-67dcd86d64-lr7dw   10.42.0.8
demo-server-67dcd86d64-pt7s9   10.42.0.7
demo-server-67dcd86d64-vs25p   10.42.0.10
demo-server-67dcd86d64-xlnnv   10.42.0.6
```

#### podとIPアドレス (jsonpath)

```console
$ kubectl get pod -o jsonpath='{range .items[*]}{.metadata.name}{","}{.status.podIP}{"\n"}{end}'
demo-server-67dcd86d64-5fg4r,10.42.0.5
demo-server-67dcd86d64-cqh5t,10.42.0.11
demo-server-67dcd86d64-gclqc,10.42.0.15
demo-server-67dcd86d64-hr9nw,10.42.0.7
demo-server-67dcd86d64-htgfs,10.42.0.14
demo-server-67dcd86d64-jxkbp,10.42.0.16
demo-server-67dcd86d64-ngc6t,10.42.0.13
demo-server-67dcd86d64-nwhpd,10.42.0.12
demo-server-67dcd86d64-tg672,10.42.0.4
demo-server-67dcd86d64-wzhs9,10.42.0.8
```

### rolebindings / clusterrolebindings

紐付いてるServiceAccountを確認する

```console
kubectl get rolebindings,clusterrolebindings -A -o custom-columns='KIND:kind,NAMESPACE:metadata.namespace,NAME:metadata.name,SERVICE_ACCOUNTS:subjects[?(@.kind=="ServiceAccount")].name' | grep <SERVICE_ACCOUNT_NAME>
```

### jsonpathでドットを含むキー

キーに使うドットは`\`でエスケープする。

```console
kubectl get secret credential -o jsonpath='{.data.\.dockerconfigjson}'
```

あるいは

```console
kubectl get secret credential -o jsonpath="{.data['\.dockerconfigjson']}"
```

シングルクォートとダブルクォートは逆だとダメ(辞書のアクセスはシングルクォートを使う)

```console
kubectl get secret credential -o jsonpath='{.data["\.dockerconfigjson"]}'
error: error parsing jsonpath {.data["\.dockerconfigjson"]}, invalid array index "\.dockerconfigjson"
```

## rollout

### restart

podの再作成 (delete podでも同じ)

```console
kubectl rollout restart deployment <resource-name>
```

## label

リソースのラベルを追加/変更/削除する

### 追加

```console
$ kubectl label pod rsync-pod app=rsync
pod/rsync-pod labeled
$ kubectl get pod --show-labels
NAME                       READY   STATUS    RESTARTS   AGE   LABELS
rsync-pod                  1/1     Running   3          28d   app=rsync
sockserv-db66b7df7-rtcv9   1/1     Running   1          17d   app=sockserv,pod-template-hash=db66b7df7
```

更に追加

```console
$ kubectl label pod rsync-pod state=normal
pod/rsync-pod labeled
$ kubectl get pod -l app=rsync --show-labels
NAME        READY   STATUS    RESTARTS   AGE   LABELS
rsync-pod   1/1     Running   3          28d   app=rsync,state=normal
```

複数まとめても可

```console
$ kubectl label node k3s-node1 label1=foo label2=bar label3=baz
node/k3s-node1 labeled
```

### 更新

既に存在する場合はエラーになる。

```console
$ kubectl label pod rsync-pod state=error
error: 'state' already has a value (normal), and --overwrite is false
```

が、メッセージにある通り`--overwrite`を追加すれば上書きできる。

```console
kubectl label pod rsync-pod state=error --overwrite
pod/rsync-pod labeled
kubectl get pod -l app=rsync --show-labels
NAME        READY   STATUS    RESTARTS   AGE   LABELS
rsync-pod   1/1     Running   3          28d   app=rsync,state=error
```

### 削除

ラベルのキーに`-`を付与したものをセットすれば削除される。

```console
kubectl label pod rsync-pod state-
```

## drain

大抵はDaemonSetのpodが動いているので`--ignore-daemonsets`オプションを付ける。

```console
kubectl drain k3s-node2 --force --ignore-daemonsets
```

ノードを完全にクラスタから除外するにはさらに`kubectl delete node <node-name>`する。

## KUBECONFIG

### 証明書検証無視

<https://kubernetes.io/docs/reference/config-api/kubeconfig.v1/#Cluster>

```yaml
apiVersion: v1
clusters:
- cluster:
    # certificate-authority-data: LS0tL...
    server: https://my-cluster.example.org:6443
    insecure-skip-tls-verify: true
  name: my-cluster

# snip...
```

`insecure-skip-tls-verify: true`を使用する。  
ただしその時`certificate-authority-data`は未定義にしておく。

[Kubernetes:Why does the 'insecure-skip-tls-verify' in kubeconfig not work? - Stack Overflow](https://stackoverflow.com/questions/33049185/kuberneteswhy-does-the-insecure-skip-tls-verify-in-kubeconfig-not-work)
