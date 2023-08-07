# ESXi

## OVFエクスポート

```console
ovftool.exe --noImageFiles vi:<ESXiユーザー名>:<ESXiパスワード>@<ESXiアドレス>/<VM名> エクスポート先パス
```

実行例

```console
 .\ovftool.exe --noImageFiles vi://root:********@192.168.*.*/cloud-dev E:\data\image
```
