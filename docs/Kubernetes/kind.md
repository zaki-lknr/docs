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

## マルチノード

ノード設定用のファイル作成する。

```yaml
# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

で、このファイルを指定してクラスタ作成する。

```console
$ kind create cluster --config kind-config.yaml
```

## クラスタバージョンの指定

[Releases · kubernetes-sigs/kind](https://github.com/kubernetes-sigs/kind/releases)

リリースノートに各種バージョンのコンテナIDの記載があるのでそれを指定する。  
v1.19ならこんな感じ。

```yaml
# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.19.11@sha256:07db187ae84b4b7de440a73886f008cf903fcf5764ba8106a9fd5243d6f32729
- role: worker
  image: kindest/node:v1.19.11@sha256:07db187ae84b4b7de440a73886f008cf903fcf5764ba8106a9fd5243d6f32729
- role: worker
  image: kindest/node:v1.19.11@sha256:07db187ae84b4b7de440a73886f008cf903fcf5764ba8106a9fd5243d6f32729
```
