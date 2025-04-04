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
