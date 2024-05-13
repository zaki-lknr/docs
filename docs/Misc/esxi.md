# ESXi

## ovftool

この辺から。

- 4.4.0: [Open Virtualization Format Tool (ovftool) - VMware {code}](https://developer.vmware.com/web/tool/4.4.0/ovf)
- 4.6.0: [Open Virtualization Format (OVF) Tool - VMware {code}](https://developer.vmware.com/web/tool/4.6.0/ovf-tool)

### OVFエクスポート

```console
ovftool.exe --noImageFiles vi:<ESXiユーザー名>:<ESXiパスワード>@<ESXiアドレス>/<VM名> エクスポート先パス
```

実行例

```console
 .\ovftool.exe --noImageFiles vi://root:********@192.168.*.*/cloud-dev E:\data\image
```
