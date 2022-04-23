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
