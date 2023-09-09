# QNAP

## 外付けUSB HDD

繋げばそのまま使える。少なくともEXT4ではLinuxで使っていたファイルをそのままマウント・共有できる。  
ただしストレージプールには使えない。

共有名はデフォルトではパーティションのラベル名。  
変更するには「ストレージ＆スナップショット」の「ストレージ情報」から。ただし変更すると各種設定がリセットされる。

CIFS共有時のコメントを設定するには「コントロールパネル」->「共有フォルダ―」の「プロパティの編集」から。  
でもリブートしたら消える？

## OVFインポート

ESXiなどからエクスポートしたovfファイルは `qm` コマンドでインポートする。

```console
qm importovf <VM ID> <OVFファイルのパス> <VM用ストレージ名>
```

実行例

```console
root@pve:~# qm importovf 111 /mnt/pve/pecorino/archive/export/cheddar/cheddar.ovf local-lvm
```
