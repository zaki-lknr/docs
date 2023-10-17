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
