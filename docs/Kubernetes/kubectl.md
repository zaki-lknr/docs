# kubectl

分類変えるかも…

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

## create

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

## run

### ワーク用のpodをデプロイする

```console
$ kubectl run work-pod --image=fedora --command -- tail -f /dev/null
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

## delete

### 指定namespaceの指定typeを全部消す

```console
$ kubectl delete pvc -n zzz --all
```
