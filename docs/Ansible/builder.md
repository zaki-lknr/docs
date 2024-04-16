# Ansible Builder

## ansible-builder

### タグ指定

```console
$ ansible-builder build -t net-ee:latest
Running command:
  podman build -f context/Containerfile -t net-ee:latest context
Complete! The build context can be found at: /home/zaki/local/ansible-builder/net-runner/context
```

`image-name:tag`のみだと、`localhost/`が自動で付与される。

```console
$ podman image ls | grep net
localhost/net-ee                 latest             119f99382b23  7 minutes ago   819 MB
```

`repository-name/image-name:tag`を指定すると…

```console
$ ansible-builder build -t zakihmkc/net-ee:latest
Running command:
  podman build -f context/Containerfile -t zakihmkc/net-ee:latest context
Complete! The build context can be found at: /home/zaki/local/ansible-builder/net-runner/context
```

```console
$ podman image ls | grep zakihmkc
localhost/zakihmkc/net-ee        latest             119f99382b23  9 minutes ago   819 MB
```

それでも`localhost/`が付与された。

レジストリURLを付与すると、

```console
$ ansible-builder build -t docker.io/zakihmkc/net-ee:latest
Running command:
  podman build -f context/Containerfile -t docker.io/zakihmkc/net-ee:latest context
Complete! The build context can be found at: /home/zaki/local/ansible-builder/net-runner/context
$ podman image ls | grep docker
docker.io/zakihmkc/net-ee        latest             119f99382b23  13 minutes ago  819 MB
```

レジストリのURLを省略すると`localhost`が付与されるみたいね。

## ベースイメージ

### RHEL

builderイメージは [Certified container images](https://catalog.redhat.com/software/containers/search) で"ansible-builder"で検索。
だいたいRHEL8かRHEL9のイメージが出てくるのでそれを選択し、tagで1.2系か3系を選択する。(1.2系はRHEL8のみかな？)

ベースイメージはImage typeで「Automation execution environment」にチェックを入れれば出てくる。
カスタムするのであればminimalを選択して必要なパッケージを追加していけばいいと思う。
TagでAnsibleバージョンを選択する。最新はlatest

大まかなバージョン(RHEL8かRHEL9か、など)はマウスオーバーしたときのURLで判断できる。(rhel8やrhel9がパスに入っている)
