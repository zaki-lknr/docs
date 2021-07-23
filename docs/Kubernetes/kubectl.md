# kubectl

分類変えるかも…

## タブ補完

```bash
source <(kubectl completion bash)
complete -o default -F __start_kubectl kc
```

[[oc / kubectl] コマンドや引数の補完設定 【completion】 - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/03/02/205509)

`kind`や`helm`も同じ要領。

## create

### secret

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
