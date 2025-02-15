# K3s

## docs

- [K3s - Lightweight Kubernetes | K3s](https://docs.k3s.io/)
- [日本語版 K3sマニュアル](https://www.rancher.co.jp/pdfs/K3s-eBook4Styles0507.pdf)

## インストール

### 準備

#### Raspberry Pi

デフォルト状態でインストールしようとしても下記メッセージが出力されて起動失敗する。

> [INFO]  Failed to find memory cgroup, you may need to add "cgroup_memory=1 cgroup_enable=memory" to your linux cmdline (/boot/cmdline.txt on a Raspberry Pi)

インストールドキュメントにも同じことが書かれている。  
<https://docs.k3s.io/installation/requirements?os=pi>

`/boot/cmdline.txt`(中身に`moved to /boot/firmware/cmdline.txt`と書かれていればそっち)を編集して以下を末尾に追記。

```text
cgroup_memory=1 cgroup_enable=memory
```

### server(control plane)

```console
curl -sfL https://get.k3s.io | sh -
```

### agent(worker node)

```console
curl -sfL https://get.k3s.io | K3S_URL=https://server:6443 K3S_TOKEN=${token} sh -
```

tokenはserverの `/var/lib/rancher/k3s/server/agent-token` にある。

### バージョン指定

バージョン番号を指定する場合

```console
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.26.9+k3s1  sh -
```

チャンネルを指定する場合

```console
curl -sfL https://get.k3s.io | INSTALL_K3S_CHANNEL=latest sh -
```

チャンネルはデフォルト`stable`で1個前のメジャーバージョンだったりする。  
[チャンネル一覧](https://update.k3s.io/v1-release/channels)

### 起動パラメタ

- [server | K3s](https://docs.k3s.io/cli/server)
- [agent | K3s](https://docs.k3s.io/cli/agent)
- [Configuration Options | K3s](https://docs.k3s.io/installation/configuration)
- [Network Options | K3s](https://docs.k3s.io/installation/network-options)

### デフォルト値

| 項目          | 値            |
| ----------- | ------------ |
| Pod IPs     | 10.42.0.0/16 |
| Service IPs | 10.43.0.0/16 |
| cluster dns | 10.43.0.10   |

### ネットワーク

#### IPアドレス

| 項目          | 変更方法                      |
| ----------- | ------------------------- |
| Pod IPs     | --cluster-cidr 0.0.0.0/16 |
| Service IPs | --service-cidr 0.0.0.0/16 |

```console
curl -sfL https://get.k3s.io | sh -s - \
  --cluster-cidr "172.28.0.0/16" \
  --service-cidr "172.29.0.0/16" \
  --kube-controller-manager-arg=node-cidr-mask-size=24 \
  --kubelet-arg=max-pods=200
```

#### resolv.conf

```
$ cat /etc/k3s.resolve.conf 
nameserver 8.8.8.8
$ curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644 --resolv-conf /etc/k3s.resolv.conf 
```

#### デプロイ後のアドレス変更は不可

> You cannot change the CIDRs or add a new address family to an existing cluster. Cluster networking should be established when the cluster is initially started.
>
> https://github.com/k3s-io/k3s/issues/93#issuecomment-1551967292

systemdユニットファイルに記載のアドレス指定部分を変更しても、クラスターには反映されず動作が不安定になる

### コンポーネントの無効化

基本的にデフォルトon  
リソース節約など使用しないものはoffにできる。

Traefik Ingress Controllerとlocal storageをオフにする場合のインストールオプションは以下。

```console
curl -sfL https://get.k3s.io | sh -s - --disable traefik --disable local-storage
```

### インストールパラメタの設定ファイル化

`/etc/rancher/k3s/config.yaml`ファイルに定義する。

```yaml
---
write-kubeconfig-mode: "0644"
cluster-cidr: "172.28.0.0/16"
service-cidr: "172.29.0.0/16"
kube-controller-manager-arg:
  - node-cidr-mask-size=24
kubelet-arg:
  - max-pods=200
  - kube-reserved=cpu=100m,memory=100Mi
  - system-reserved=cpu=100m,memory=100Mi
```

## アップグレード

[Manual Upgrades | K3s](https://docs.k3s.io/upgrades/manual)

インストール時に指定した環境変数・パラメタを指定しつつ、インストールスクリプトを追加実行すればアップグレードできる。

```console
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=vX.Y.Z+k3s1 <EXISTING_K3S_ENV> sh -s - <EXISTING_K3S_ARGS>
```

マイナーバージョンのskipはサポート外なので、リリースチャンネル指定よりもバージョン指定の方がやりやすいと思う。

> Ensure that your plan does not skip intermediate minor versions when upgrading.

## 停止

k3sサービス自体は`systemctl`で停止できAPIエンドポイントなどは停止されるが実はコンテナは動作したまま。
コンテナも停止するには`k3s-killall.sh`を実行する。

[Stopping K3s | K3s](https://docs.k3s.io/upgrades/killall)

## 証明書

[certificate | K3s](https://docs.k3s.io/cli/certificate)

デフォルトではデプロイ時起点で1年の証明書が作成される。90日を切った時点でクラスターを再起動するとその日から1年の期限の新しい証明書にローテートされる。  
強制的にローテートしたい場合は`k3s certificate rotate`を実行し再起動する。

期限をカスタマイズする場合は環境変数 `CATTLE_NEW_SIGNED_CERT_EXPIRATION_DAYS` を使用する。  
[[Kubernetes] K3sにおける証明書の期限の延長や設定（平たく言うと塩漬け運用したい場合のｘ年設定）について - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2024/01/24/221706)

## LoadBalancer Service

### 特定のノードのみでlistenするには

ノードに以下のラベルを付与するとallow-list modeになり、このラベルが付いたノードのみでlistenするようになる

```console
svccontroller.k3s.cattle.io/enablelb=true
```

## オフライン環境

### プライベートレジストリ設定

[Private Registry Configuration | K3s](https://docs.k3s.io/installation/private-registry)

プライベートレジストリを利用するには「デフォルトで`docker.io`向きになっている通信をローカルにフォワードする」設定を追加する。
ローカル環境にたてたGitLabのコンテナレジストリを使う場合でパスが深くなる場合も`rewrite`を使って調整可能。(イメージ名は変更しない)

`/etc/rancher/k3s/registries.yaml`に以下のように設定。  
証明書の検証をスキップする場合は`insecure_skip_verify`を追加する。

```yaml
mirrors:
  docker.io:
    endpoint:
      - "https://gitlab.example.org:25000"
    rewrite:
      "^rancher/(.*)": "zaki/images/$1"
configs:
  "gitlab.example.org:25000":
    tls:
      insecure_skip_verify: true
```

また、この設定はノード毎に必要なので、マルチノード構成の場合は全てのノードに設定する。

## K3d (compose)

[k3s/docker-compose.yml at master · k3s-io/k3s](https://github.com/k3s-io/k3s/blob/master/docker-compose.yml)

### ワーカーのスケーリング

初期構築時であれば`--scale`で指定できる

```console
$ docker compose up -d --scale agent=5
[+] Running 6/8
 ⠸ Network k3s-compose_default      Created                                                                 1.3s 
 ⠹ Volume "k3s-compose_k3s-server"  Created                                                                 1.2s 
 ✔ Container k3s-compose-server-1   Started                                                                 0.4s 
 ✔ Container k3s-compose-agent-5    Started                                                                 1.2s 
 ✔ Container k3s-compose-agent-2    Started                                                                 1.0s 
 ✔ Container k3s-compose-agent-3    Started                                                                 0.8s 
 ✔ Container k3s-compose-agent-1    Started                                                                 0.6s 
 ✔ Container k3s-compose-agent-4    Started                                                                 0.4s 

$ kubectl get node
NAME           STATUS   ROLES                  AGE   VERSION
4c1f8de42bfe   Ready    control-plane,master   20s   v1.29.1+k3s2
2f5893c02aa3   Ready    <none>                 9s    v1.29.1+k3s2
8239e04d52ef   Ready    <none>                 19s   v1.29.1+k3s2
9952de1bc166   Ready    <none>                 12s   v1.29.1+k3s2
412b213f3d10   Ready    <none>                 12s   v1.29.1+k3s2
b513c8231aad   Ready    <none>                 11s   v1.29.1+k3s2
```

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

## アンインストール

```console
$ k3s-uninstall.sh 
```
