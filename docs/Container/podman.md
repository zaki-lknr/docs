# Podman

## docs

[podman/troubleshooting.md at main · containers/podman](https://github.com/containers/podman/blob/main/troubleshooting.md)

## podmanコマンド

### save

複数イメージのtar保存

```console
podman image save -o output.tar -m image1:tag image2:tag
```

`-m`が無いと最初のイメージのみになる。

### push

ローカルのイメージをpush

```console
podman push localhost/image:tag docker://remote.example.org/image:tag
```

push先を第2引数で指定できるのでtag(FQDN)の追加を行っておく必要はない。

#### TLS検証無視

`--tls-verify=false`を付与する。

```console
podman push registry.example.org/path/to/image:latest --tls-verify=false
```

### ps --external

Podman外(buildahビルドなど)のコンテナもリストアップする

```console
zaki@cloud-dev2:~$ podman ps 
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES

zaki@cloud-dev2:~$ podman ps --external 
CONTAINER ID  IMAGE                                                                                          COMMAND     CREATED        STATUS      PORTS       NAMES
3f09ea013c86  docker.io/library/almalinux:9                                                                  buildah     21 months ago  Storage                 almalinux-working-container
7139e02472ae  docker.io/library/7420bdd2d63d87eb65afecc773f10d03de92d51098771d97746425c3043c3d95-tmp:latest  buildah     21 months ago  Storage                 83bdddbf089b-working-container
76fa5ec79fb1  docker.io/library/eedc67f030eef81a46a39d553e82b5494e9602a619f248dd2dd377ea1194d536-tmp:latest  buildah     21 months ago  Storage                 c284335d223f-working-container
:
:
```

## volume

### permission denied

だいたいSELinux周り。`Z`をつければOK

```console
podman run -d --name serv1 -p 8080:80 -v ./serv1:/usr/local/apache2/htdocs/:Z httpd:2.4
```

## service

### OS起動時にコンテナ起動するには

`podman-restart.service`を使用する(デフォルト無効)

```console
systemctl enable --now podman-restart.service
```

これでrootユーザーのコンテナは`--restart=always`が有効(OS起動時にコンテナ起動)になる。
