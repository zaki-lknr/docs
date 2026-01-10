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

### stats

リソース使用状況を確認。  
`docker stats`とだいたい同じ。

```console
ID            NAME         CPU %       MEM USAGE / LIMIT  MEM %       NET IO             BLOCK IO    PIDS        CPU TIME    AVG CPU %
e73ceb58e96c  jolly_morse  0.00%       397.3kB / 8.045GB  0.00%       1.098kB / 9.082kB  0B / 0B     1           6.895138s   0.00%
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

## 設定

### insecureなレジストリ設定

ユーザー単位であれば`$HOME/.config/containers/registries.conf`に以下作成

```ini
[registries.insecure]
registries = ['my-registry.example.org:5000']
```

システムワイドであれば`/etc/containers/registries.conf`または`/etc/containers/registries.conf.d/insecure-registries.conf`などに作成してもよい。

> Error: loading drop-in registries configuration "/etc/containers/registries.conf.d/insecure-registries.conf": registry must be in v2 format but is in v1

エラーが出る場合、v2の書式で作成する。

```toml
[[registry]]
location = "my-registry.example.org:5000"
insecure = true
```
