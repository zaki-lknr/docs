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
