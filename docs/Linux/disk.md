# ディスク

## フォーマット確認

`df -T`を実行

```console
$ df -T
ファイルシス            タイプ   1K-ブロック     使用   使用可 使用% マウント位置
devtmpfs                devtmpfs        4096        0     4096    0% /dev
tmpfs                   tmpfs        3927684        0  3927684    0% /dev/shm
tmpfs                   tmpfs        1571076     1280  1569796    1% /run
/dev/mapper/centos-root xfs        121047300 95026992 26020308   79% /
tmpfs                   tmpfs        3927688        0  3927688    0% /tmp
/dev/sda1               xfs          1038336   319000   719336   31% /boot
tmpfs                   tmpfs         785536        0   785536    0% /run/user/1000
```

```console
$ df -T
Filesystem                Type  1K-blocks      Used Available Use% Mounted on
tmpfs                     tmpfs    804648      2192    802456   1% /run
/dev/mapper/vgubuntu-root ext4  120977096  49076772  65708812  43% /
tmpfs                     tmpfs   4023240         0   4023240   0% /dev/shm
tmpfs                     tmpfs      5120         4      5116   1% /run/lock
/dev/sda1                 vfat     523248         4    523244   1% /boot/efi
tmpfs                     tmpfs    804648       148    804500   1% /run/user/1000
/dev/sdb2                 ext4  479594152 178667716 276490880  40% /media/zaki/376b22ca-a013-11e8-81fd-000180792463
```

## ディスク追加

USB接続の外付けHDDが死にかけてたので交換してデータ移行。

### HDD繋ぐ

デバイスの番号は`dmesg`で確認

```
[  268.149764] sd 6:0:0:0: Attached scsi generic sg1 type 0
[  271.516612] sd 6:0:0:0: [sdb] 3907029168 512-byte logical blocks: (2.00 TB/1.82 TiB)
[  271.517672] sd 6:0:0:0: [sdb] Write Protect is off
[  271.517678] sd 6:0:0:0: [sdb] Mode Sense: 73 00 10 08
[  271.518739] sd 6:0:0:0: [sdb] Write cache: enabled, read cache: enabled, supports DPO and FUA
[  271.543154]  sdb: sdb1
[  271.546268] sd 6:0:0:0: [sdb] Attached SCSI disk
```

`fdisk -l`で確認

```
root@zaki-CF-J10YYBHR:~# fdisk -l /dev/sdb
ディスク /dev/sdb: 1.84 TiB, 2000398934016 バイト, 3907029168 セクタ
Disk model: HD-LDS-A        
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0x0b27d361

デバイス   起動 開始位置   最後から     セクタ サイズ Id タイプ
/dev/sdb1             64 3907029119 3907029056   1.8T  7 HPFS/NTFS/exFAT
```

### 既存パーティション削除

```
root@zaki-CF-J10YYBHR:~# fdisk /dev/sdb

fdisk (util-linux 2.34) へようこそ。
ここで設定した内容は、書き込みコマンドを実行するまでメモリのみに保持されます。
書き込みコマンドを使用する際は、注意して実行してください。


コマンド (m でヘルプ):
```

```
コマンド (m でヘルプ): p
ディスク /dev/sdb: 1.84 TiB, 2000398934016 バイト, 3907029168 セクタ
Disk model: HD-LDS-A        
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0x0b27d361

デバイス   起動 開始位置   最後から     セクタ サイズ Id タイプ
/dev/sdb1             64 3907029119 3907029056   1.8T  7 HPFS/NTFS/exFAT

コマンド (m でヘルプ): d
パーティション 1 を選択
パーティション 1 を削除しました。

コマンド (m でヘルプ): p
ディスク /dev/sdb: 1.84 TiB, 2000398934016 バイト, 3907029168 セクタ
Disk model: HD-LDS-A        
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0x0b27d361

コマンド (m でヘルプ): 
```

パーティションが1個しかないからか、`d`でｼｭｯと消えた。

### パーティション作成

```
コマンド (m でヘルプ): n
パーティションタイプ
   p   基本パーティション (0 プライマリ, 0 拡張, 4 空き)
   e   拡張領域 (論理パーティションが入ります)
選択 (既定値 p): 

既定の回答 p であるものとみなします。
パーティション番号 (1-4, 既定値 1): 
最初のセクタ (2048-3907029167, 既定値 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-3907029167, 既定値 3907029167): 

新しいパーティション 1 をタイプ Linux、サイズ 1.8 TiB で作成しました。

コマンド (m でヘルプ): p
ディスク /dev/sdb: 1.84 TiB, 2000398934016 バイト, 3907029168 セクタ
Disk model: HD-LDS-A        
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0x0b27d361

デバイス   起動 開始位置   最後から     セクタ サイズ Id タイプ
/dev/sdb1           2048 3907029167 3907027120   1.8T 83 Linux

コマンド (m でヘルプ): 
```

### 書き込み

```
コマンド (m でヘルプ): w
パーティション情報が変更されました。
Failed to remove partition 1 from system: デバイスもしくはリソースがビジー状態です
Failed to add partition 1 to system: デバイスもしくはリソースがビジー状態です

The kernel still uses the old partitions. The new table will be used at the next reboot. 
ディスクを同期しています。

root@zaki-CF-J10YYBHR:~#
```

あー、自動マウントされたままだったｗｗｗ
`reboot`しよう。

reboot前

```
root@zaki-CF-J10YYBHR:~# fdisk -l /dev/sdb
ディスク /dev/sdb: 1.84 TiB, 2000398934016 バイト, 3907029168 セクタ
Disk model: HD-LDS-A        
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0x0b27d361

デバイス   起動 開始位置   最後から     セクタ サイズ Id タイプ
/dev/sdb1           2048 3907029167 3907027120   1.8T 83 Linux
```

### ext4フォーマット

`mkfs -t ext4`

```
root@zaki-CF-J10YYBHR:~# time mkfs -t ext4 /dev/sdb1
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 488378390 4k blocks and 122101760 inodes
Filesystem UUID: fa5897f2-39b1-4ea4-937f-b63b7e34571f
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
        102400000, 214990848

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): 
done
Writing superblocks and filesystem accounting information:            
done


real    0m16.078s
user    0m0.045s
sys     0m0.171s
```

### ラベル設定

`e2label`で設定・確認。

```
root@zaki-CF-J10YYBHR:~# e2label /dev/sdb1 "HD-LDS-A"
root@zaki-CF-J10YYBHR:~# e2label /dev/sdb1
HD-LDS-A
```

## LVM

### LVの論理ボリュームのリサイズ

現在のサイズから+10GB拡張する。

```console
# lvresize -r -L +10G /dev/mapper/centos-root
```

## fsck

### cleanでも強制スキャン

`-f`を付与

```console
fsck.ext4 -f /dev/mapper/ubuntu--vg-ubuntu--lv
```

### 起動時に強制fsck

`shutdown`のオプションに`-F`を付与

```console
shutdown -F -r now
```

## mount

### CIFS

```console
[root@cloud-dev2 ~]# mount -t cifs -o username=zaki,uid=***,gid=*** //pecorino/HD-LDS-A /mnt/
Password for zaki@//pecorino/HD-LDS-A: 
```

`uid`と`gid`も指定しないとownerの設定ができない(mountした時のもので固定される)

### NFS

```console
mount -t nfs -o rw pecorino:/dev /mnt/
```

## fstab

```
/dev/mapper/centos-root  /      xfs   defaults  0 1
192.168.0.200:/share     /mnt   nfs   rw        0 0
```

フィールドは左から順に

1. デバイス
1. マウントポイント
1. ファイルシステムタイプ
1. オプション
1. `dump`による出力対象フラグ(0:対象外)
1. 起動時`fsck`の対象フラグ(0:対象外)
