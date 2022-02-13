# K3s

## Always FreeのOCI(A1.Flex 2cpu/12gb ram)にシングルノードクラスタ作る

標準のE2.1.Microの場合↓  
[[Oracle Cloud] K3sでAlways Free枠ノードにKubernetesクラスタ作成してローカルからkubectlアクセスまで - Qiita](https://qiita.com/zaki-lknr/items/e829a3ad548e362ef89c)

```console
$ curl -sfL https://get.k3s.io | sh -
```

たまたま作成できた2cpus/12GB RAMで作成

```console
[opc@instance-20210704-1554 ~]$ curl -sfL https://get.k3s.io | sh -

:
:

インストール:
  k3s-selinux.noarch 0:0.3-0.el7

依存性関連をインストールしました:
  container-selinux.noarch 2:2.119.2-1.911c772.el7_8

完了しました!
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Creating /usr/local/bin/ctr symlink to k3s
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink from /etc/systemd/system/multi-user.target.wants/k3s.service to /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s
```

### kubeconfig

```console
[opc@instance-20210704-1554 ~]$ sudo cp /etc/rancher/k3s/k3s.yaml ~
[opc@instance-20210704-1554 ~]$ ll
合計 4
-rw-------. 1 root root 2961  7月 23 02:30 k3s.yaml
[opc@instance-20210704-1554 ~]$ sudo hhown opc:opc k3s.yaml 
```

手元にコピー

```console
$ scp oci-ap-n1:k3s.yaml oci-ap-n1-k3s.yaml
```

`server`のアドレスをグローバルIPに変更し、`insecure-skip-tls-verify: true`を追加。  
(※うまくいかないときは`kubectl`に`--insecure-skip-tls-verify=true`オプションを追加)

```yaml
clusters:
- cluster:
    certificate-authority-data: ...
    server: https://<hostのgip>:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
  insecure-skip-tls-verify: true
```

### 穴あけ

```console
[opc@instance-20210704-1554 ~]$ sudo firewall-cmd --add-port=6443/tcp --permanent
success
[opc@instance-20210704-1554 ~]$ sudo firewall-cmd --reload
success
```

あとwebコンソールで、6443/TCPへのアクセス許可を追加。

### deploy sample

```console
[zaki@cloud-dev kubeconfig]$ KUBECONFIG=oci-ap-n1-k3s.yaml kubectl apply -n sample -f ~/src/k8s-samples/sample-web/httpd-clusterip/sample-http.yaml 
deployment.apps/sample-http created
service/sample-http created
[zaki@cloud-dev kubeconfig]$ KUBECONFIG=oci-ap-n1-k3s.yaml kubectl get pod -n sample
NAME                           READY   STATUS              RESTARTS   AGE
sample-http-6c94f59975-65p6w   0/1     ContainerCreating   0          9s
sample-http-6c94f59975-clzhp   0/1     ContainerCreating   0          9s
```

```console
[zaki@cloud-dev kubeconfig]$ KUBECONFIG=oci-ap-n1-k3s.yaml kubectl get pod -n sample -o wide
NAME                           READY   STATUS    RESTARTS   AGE   IP           NODE                     NOMINATED NODE   READINESS GATES
sample-http-6c94f59975-65p6w   1/1     Running   0          87s   10.42.0.9    instance-20210704-1554   <none>           <none>
sample-http-6c94f59975-clzhp   1/1     Running   0          87s   10.42.0.10   instance-20210704-1554   <none>           <none>
```

### ノードスペック

```console
[zaki@cloud-dev kubeconfig]$ KUBECONFIG=oci-ap-n1-k3s.yaml kubectl describe node

[...]

Capacity:
  cpu:                2
  ephemeral-storage:  40223552Ki
  hugepages-16Gi:     0
  hugepages-2Mi:      0
  hugepages-512Mi:    0
  memory:             11125568Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  39129471355
  hugepages-16Gi:     0
  hugepages-2Mi:      0
  hugepages-512Mi:    0
  memory:             11125568Ki
  pods:               110
```

おぉ、CPUはともかくメモリが潤沢だ。

rebootしてもOK
