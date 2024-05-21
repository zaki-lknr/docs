# Linux

## いろいろメモ

[Rufus - 起動可能なUSBドライブを簡単に作成できます](https://rufus.ie/ja/)

Rufus使用  
[WindowsでUbuntuのUSBブートメディアを作成する手順 | 机上の自論](https://kijonojiron.com/ubuntu_usb_boot_media_create)

Universal USB Installer  
[Linux UbuntuのブータブルUSBドライブを作成する手順 | 俺の開発研究所](https://itlogs.net/ubuntu-bootable-usb/)

## sudo

パスワード不要にする雑な設定

```
# cat /etc/sudoers.d/nopass 
%wheel ALL=(ALL) NOPASSWD: ALL
```

Debian系はグループ名は`wheel`でなく`sudo`になる。

```
%sudo ALL=(ALL) NOPASSWD: ALL
```

### コマンドでパス不要設定

rhel系

```command
cat <<__EOL__ > /etc/sudoers.d/nopass
%wheel ALL=(ALL) NOPASSWD: ALL
__EOL__
```

debian系

```command
cat <<__EOL__ > /etc/sudoers.d/nopass
%sudo ALL=(ALL) NOPASSWD: ALL
__EOL__
```

## 環境変数

### 設定

```console
export PATH=$PATH:~/bin
```

### 削除

環境変数`EDITOR`を削除するには

```console
export -n EDITOR
```

シェル変数に残るので`echo $EDITOR`の出力は変化しないが環境変数としては消える。

## シングルユーザーモード

### ubuntu

GRUBメニュー画面で`e`し、

```text
setparams 'Ubuntu'

        recordfail
        load_video
        gfxmode $linux_gfx_mode

        [...]

        linux   /vmlinuz-... root=/dev/... ...
```

の`linux`で始まる行の末尾に`single`を追記して`Ctrl-x`で起動する。  
ルートファイルシステムをmountしたくない場合は`single`でなく`break`を追記。

## GRUB

ブートローダーが壊れてgrub rescueモードになった場合。  
まず`ls`で/bootがあるパーティションを探す。

```console
grub rescue> ls
(hd0) (hd0,gpt3) (hd0,gpt2) (hd0,gpt1)
grub rescue> ls (hd0,gpt1)
(hd0,gpt3): Filesystem is unknown.
grub rescue> ls (hd0,gpt3)
(hd0,gpt3): Filesystem is ext2.
grub rescue> ls (hd0,gpt3)/
./ ../ lost+found/ bin boot/ dev/ etc/ home/ lib lib32 lib64 libx32 media/ mnt/
opt/ proc/ root/ run/ sbin snap/ srv/ sys/ tmp/ usr/ var/
grub rescue>
```

みつかったら`set prefix`

```console
grub rescue>set prefix=(hd0,gpt3)/boot/grub
grub rescue>set root=(hd0,gpt3)
grub rescue>insmod normal
grub rescue>insmod linux
grub rescue>normal
```

これでブートできる。  
Linuxが起動したらGRUBを再インストール

```console
sudo update-grub
sudo grub-install /dev/sda
```

## アップグレード

### Fedora

[Upgrading Fedora Using DNF System Plugin :: Fedora Docs](https://docs.fedoraproject.org/en-US/quick-docs/upgrading-fedora-offline/)

- `sudo dnf upgrade --refresh`, `sudo reboot`
- `sudo dnf install dnf-plugin-system-upgrade`
- `sudo dnf system-upgrade download --releasever=39`  # 39はターゲットバージョン。飛び級がサポートされるのは2段階(37 -> 39)
- `sudo dnf system-upgrade reboot`

### Ubuntu

- `sudo apt-get update`
- `sudo apt-get upgrade`
- `sudo apt-get dist-upgrade`
- `sudo apt-get autoremove`
- `sudo do-release-upgrade`

```console
If you continue, an additional ssh daemon will be started at port '1022'. 
Do you want to continue? 
```

SSH経由だと上記警告が出るが続けるなら続ける。
あとは進めていくと確認が出るので都度進める。
