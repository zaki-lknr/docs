# Podman

## podmanコマンド

### save

複数イメージのtar保存

```console
podman image save -o output.tar -m image1:tag image2:tag
```

`-m`が無いと最初のイメージのみになる。
