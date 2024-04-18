# Podman

## podmanコマンド

### save

複数イメージのtar保存

```console
podman image save -o output.tar -m image1:tag image2:tag
```

`-m`が無いと最初のイメージのみになる。

## volume

### permission denied

だいたいSELinux周り。`Z`をつければOK

```console
podman run -d --name serv1 -p 8080:80 -v ./serv1:/usr/local/apache2/htdocs/:Z httpd:2.4
```
