# kind

DockerやPodman上にKubernetesのノードとして動くコンテナをデプロイ

[kind](https://kind.sigs.k8s.io/)

- [[Kubernetes / kind] kindを使ってDocker上にクラスタを高速作成し、MetalLBも併用してクラスタ横断した通信お試し - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/08/01/135922)

## クラスタ作成

基本 (`kind`という名前のクラスタを作成すｒ)

```console
$ kind create cluster
```

名前指定

```console
$ kind create cluster --name sample-cluster
```

## 停止

kindクラスタの停止は無い。  
クラスタを削除するか、ノードとして動作してるコンテナを停止する。

コンテナを停止するには`docker stop`するが、停止対象コンテナは`kind get nodes`で取得できる。

```console
$ kind get nodes
kind-control-plane
```

引数無しの場合は、デフォルト設定で作成したクラスタ名`kind`のノードになる。  
クラスタ名を指定する場合は

```console
$ kind get nodes --name cluster-name
```

この結果を`docker stop`に渡すために、全体としてはこんな感じ。

```console
$ kind get nodes --name cluster-name | xargs docker stop
```
