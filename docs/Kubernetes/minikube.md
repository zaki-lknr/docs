# minikube

様々な環境にk8sをインストールできる。デフォルトはDockerでk8s in docker環境。

## セットアップ

管理用コマンドをインストールする。  
[minikube start | minikube](https://minikube.sigs.k8s.io/docs/start/)

Linux/macOS/Windows用それぞれのインストール手順が用意されているので、環境に合わせたバイナリやパッケージを入手してセットアップする。

例えばLinux用バイナリを直接インストールするなら以下の通り。

```console
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

`curl -L https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 -o /path/to/minikube`して`chmod 755`でも良さそうだけど。

## クラスタ作成

```console
minikube start
```

起動に成功すると`$HOME/.kube/config`も更新(既存設定がある場合はマージ)される。

マルチノード(ノード3)の場合

```console
minikube start -n 3
```

```console
$ kubectl get node
NAME           STATUS   ROLES           AGE   VERSION
minikube       Ready    control-plane   89m   v1.27.4
minikube-m02   Ready    <none>          89m   v1.27.4
minikube-m03   Ready    <none>          88m   v1.27.4
```

## クラスタの状態

```console
$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

minikube-m02
type: Worker
host: Running
kubelet: Running

minikube-m03
type: Worker
host: Running
kubelet: Running
```
