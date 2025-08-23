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

### OVS Bridge作成

openvswitch-switchパッケージが必要なので、事前にノードで`apt-get install openvswitch-switch`を実行しておく。  
ネットワークメニューの「作成」で「OVS Bridge」を選択し、名前はデフォルト、コメントとIPv4/CIDRを入力して「作成」押下する。

差分が表示されるのでOKであれば画面上部の「設定を適用」押下

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

変更を適用するには画面上部の「設定を適用」押下

## ストレージ

### ディスクの消去

「ノード」→「ディスク」で対象ディスク(パーティションでなく)を選択して「ディスクの消去」でパーティション情報が消える。  
ディスクを使いまわす場合に実行する。

### ディスクの追加

ノードメニューのディスク配下、「LVM-Thin」などで「作成：〇〇」押下、空いているディスクを選択・名前を入力して「作成」押下

### VMのデータ領域を別ストレージに移動

VMのハードウェア設定で、ハードディスクを選択、「ディスクの動作」メニューの「ストレージに移動」で、ターゲットを変更することで、VMのデータ領域を別ストレージへ移動できる。

### 定義ファイル

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

### 登録済みストレージの設定変更

用途とかNFSのバージョンとかを変更するには、ノードでもストレージでもなく「データセンター」から。

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

### バックアップデータの保存先ストレージの変更

8.3.3時点でUIでの操作はなさそう。  
上記のパス情報を参照に`mv`で移動する。

## CLI

### VM操作

[qm(1)](https://pve.proxmox.com/pve-docs/qm.1.html)

### VM一覧

```console
qm list
```

ID、VM名、起動状態、RAMサイズ、ストレージサイズなどが一覧で出力される。
このコマンドはノード単位で出力される。

### VM詳細(qemu-guest-agent使用)

qemu-guest-agentが動作しているVMの情報を取得する。  
NIC情報であれば以下の通り。

```console
# qm guest cmd 710 network-get-interfaces
[
   {
      "hardware-address" : "00:00:00:00:00:00",
      "ip-addresses" : [
         {
            "ip-address" : "127.0.0.1",
            "ip-address-type" : "ipv4",
            "prefix" : 8
         },

         ...
      ]
   },
   {
      "hardware-address" : "bc:24:11:d6:b0:61",
      "ip-addresses" : [
         {
            "ip-address" : "192.168.0.201",
            "ip-address-type" : "ipv4",
            "prefix" : 24
         },
         {
            "ip-address" : "fe80::be24:11ff:fed6:b061",
            "ip-address-type" : "ipv6",
            "prefix" : 64
         }
      ],

      ...
```

使えるサブコマンドは`qm help`で確認できる。  
[qm(1)](https://pve.proxmox.com/pve-docs/qm.1.html)

(`qm guest`のヘルプは無さげ…)

### VM削除

```console
qm destroy <vmid> --destroy-unreferenced-disks=1
```

## ホスト名変更

```console
root@pve:~# hostnamectl set-hostname pve01
root@pve:~# 
```

他の変更ファイル(ファイル内にホスト名の記述があるものを探す)

- /etc/hosts
- /etc/hostname
- /etc/postfix/main.cf
- /etc/pve/storage.cfg

VMとノード定義

```console
root@pve01:~# ls -lF /etc/pve/nodes/
total 0
drwxr-xr-x 2 root www-data 0 May 14  2023 pve/
```

しばらく待機すると、新ホスト名のノードが生えるが中身はブランク

```console
root@pve01:~# ls -lF /etc/pve/nodes/
total 0
drwxr-xr-x 2 root www-data 0 May 14  2023 pve/
drwxr-xr-x 2 root www-data 0 Jan  5 14:51 pve01/
root@pve01:~# find /etc/pve/nodes/pve01/
/etc/pve/nodes/pve01/
/etc/pve/nodes/pve01/qemu-server
/etc/pve/nodes/pve01/lxc
```

念のためバックアップ

```console
root@pve01:~# cp -a /etc/pve/nodes/pve node-backup/
root@pve01:~# ls -lF node-backup/
total 4
drwxr-xr-x 6 root www-data 4096 May 14  2023 pve/
root@pve01:~# 
```

VM定義ファイルを移動

```console
root@pve01:~# mv /etc/pve/nodes/pve/qemu-server/* /etc/pve/nodes/pve01/qemu-server/
root@pve01:~#
root@pve01:~# ls /etc/pve/nodes/pve01/qemu-server/
101.conf  116.conf  201.conf  210.conf  303.conf  312.conf  703.conf  709.conf  712.conf   9002.conf  9011.conf
111.conf  119.conf  202.conf  301.conf  310.conf  313.conf  707.conf  710.conf  9000.conf  9003.conf  9020.conf
114.conf  121.conf  203.conf  302.conf  311.conf  702.conf  708.conf  711.conf  9001.conf  9010.conf
```
