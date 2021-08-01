# ディスク

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
