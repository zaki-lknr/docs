# Proxmox (PVE)

## UIでVMのIPアドレス確認

VMの「サマリー」でIPアドレスを確認するには、以下実行する

- オプションの「QEMU Guest Agent」を有効にする
- VMで`qemu-guest-agent`パッケージをインストールしサービスを有効にする

## ovfインポート

```console
qm importovf <VM ID> <OVFファイルのパス> <VM用ストレージ名>
```

実行例

```console
root@pve:~# qm importovf 201 /mnt/pve/pecorino/archive/export/fedora-node/fedora-node.ovf wdblue1000gb
  Logical volume "vm-201-disk-0" created.
transferred 0.0 B of 20.0 GiB (0.00%)
transferred 204.8 MiB of 20.0 GiB (1.00%)
transferred 409.6 MiB of 20.0 GiB (2.00%)
:
:
transferred 20.0 GiB of 20.0 GiB (100.00%)
root@pve:~# 
```

## VM定義ファイル関連

```console
root@pve:~# ls -F /etc/pve/nodes/pve/qemu-server
100.conf  102.conf  202.conf  301.conf  303.conf
101.conf  201.conf  203.conf  302.conf
```

中身は

```console
root@pve:~# cat /etc/pve/nodes/pve/qemu-server/301.conf 
boot: order=scsi0
cores: 2
memory: 4096
name: k8su-master
net0: e1000=CA:0D:8C:5E:46:C3,bridge=vmbr0,firewall=1
scsi0: wdblue1000gb:vm-301-disk-0
scsihw: pvscsi
smbios1: uuid=b9b7f350-0b74-46df-bd59-0318fc99372c
vmgenid: 5e1f1e65-0eb0-4cd1-867c-277b48d91616
```

実データはこれかな？

```console
root@pve:/# ls -l /dev/wdblue1000gb/vm-301-disk-0 
lrwxrwxrwx 1 root root 8 Aug  5 10:13 /dev/wdblue1000gb/vm-301-disk-0 -> ../dm-15
root@pve:/# ls -l /dev/dm-15
brw-rw---- 1 root disk 253, 15 Aug  5 10:13 /dev/dm-15
root@pve:/# file /dev/dm-15
/dev/dm-15: block special (253/15)
```

## ネットワーク

### ブリッジポート無しで作成したブリッジに物理ポートを追加

UIでLinux Bridgeの「ブリッジポート」にポート名を追加し「OK」押下。  
画面下部にメッセージが表示される。

```diff
## 変更を保留中 (有効にするには再起動か、 'Apply Configuration' (needs ifupdown2)を使用)
--- /etc/network/interfaces	2023-05-21 15:33:29.906403987 +0900
+++ /etc/network/interfaces.new	2023-10-17 09:10:38.701832421 +0900
@@ -29,7 +29,7 @@
 auto vmbr1
 iface vmbr1 inet static
 	address 172.16.0.1/23
-	bridge-ports none
+	bridge-ports enp3s0
 	bridge-stp off
 	bridge-fd 0
 #private-network-1
```

シェルを起動し

```console
root@pve:~# ifdown vmbr1 
root@pve:~# ifup vmbr1 
```

…だけだと反映されてなさそうだったので(`ifup enp3s0`も実施したが変化なし)rebootした。

※ `ifupdown2`というコマンドを別途インストールすれば良かったかも

## ストレージ定義ファイル

`/etc/pve/storage.cfg`にある。

```console
root@pve:~# cat /etc/pve/storage.cfg
dir: local
        path /var/lib/vz
        content iso,vztmpl,backup
        shared 0

lvmthin: local-lvm
        thinpool data
        vgname pve
        content rootdir,images

nfs: pecorino
        export /share
        path /mnt/pve/pecorino
        server *.*.*.*
        content iso,backup
        prune-backups keep-all=1
```

## バックアップとリストア

### リストア

VM削除済みの場合はCLIで実行する。  
以下はバックアップデータをVM ID:500としてリストア。

```console
root@pve:~# qmrestore /mnt/pve/pecorino-dev/dump/vzdump-qemu-500-2023_09_05-15_29_11.vma.zst 500
restore vma archive: zstd -q -d -c /mnt/pve/pecorino-dev/dump/vzdump-qemu-500-2023_09_05-15_29_11.vma.zst | vma extract -v -r /var/tmp/vzdumptmp177197.fifo - /var/tmp/vzdumptmp177197
CFG: size: 458 name: qemu-server.conf
DEV: dev_id=1 size: 17179869184 devname: drive-scsi0
CTIME: Tue Sep  5 15:29:21 2023
  Logical volume "vm-500-disk-0" created.
new volume ID is 'WDS100T2BA0A:vm-500-disk-0'
map 'drive-scsi0' to '/dev/WDS100T2BA0A/vm-500-disk-0' (write zeros = 0)
progress 1% (read 171835392 bytes, duration 1 sec)
progress 2% (read 343605248 bytes, duration 2 sec)
progress 3% (read 515440640 bytes, duration 4 sec)
:
progress 100% (read 17179869184 bytes, duration 20 sec)
total bytes read 17179869184, sparse bytes 12563259392 (73.1%)
space reduction due to 4K zero blocks 2.95%
rescan volumes...
```

ストレージはデフォルトだとバックアップ時と同じところへリストアされる？それともデフォルトのストレージかな？  
`--storage`で指定できそう

Web UIでリストアする場合は、VMのメニューでなくストレージのメニューからリストアを選ぶと、全バックアップデータが表示されるのでそこからリストアを行う。

### バックアップデータの実データ保存先

内蔵ストレージの場合は`/var/lib/vz/dump/`

```console
root@pve:~# ls /var/lib/vz/dump/
vzdump-qemu-500-2023_09_24-01_05_17.log
vzdump-qemu-500-2023_09_24-01_05_17.vma.zst
vzdump-qemu-500-2023_09_24-01_05_17.vma.zst.notes
:
:
```

外部の場合はマウントポイント配下の`dump`以下

```console
root@pve:~# ls /mnt/pve/pecorino/dump/
vzdump-qemu-116-2023_09_04-18_23_48.log
vzdump-qemu-116-2023_09_04-18_23_48.vma.zst
vzdump-qemu-116-2023_09_04-18_23_48.vma.zst.notes
vzdump-qemu-116-2023_09_05-23_58_49.log
vzdump-qemu-116-2023_09_05-23_58_49.vma.zst
vzdump-qemu-116-2023_09_05-23_58_49.vma.zst.notes
:
:
```
