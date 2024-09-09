# skopeo

## copy

ローカルのイメージをリモートへpush

```console
skopeo copy docker://localhost/image:tag docker://remote.example.org/image:tag
```

リモート間コピーも同様に可。

```console
skopeo copy docker://docker.io/alpine:3.19.3 docker://remote.example.org/mirror-images/alpine:3.19.3
```
