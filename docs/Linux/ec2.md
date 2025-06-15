# EC2

## バージョン確認

```console
[ec2-user@ip-172-26-10-149 ~]$ cat /etc/system-release
Amazon Linux release 2 (Karoo)
```

または

```console
[ec2-user@ip-172-26-10-149 ~]$ cat /etc/os-release 
NAME="Amazon Linux"
VERSION="2"
ID="amzn"
ID_LIKE="centos rhel fedora"
VERSION_ID="2"
PRETTY_NAME="Amazon Linux 2"
ANSI_COLOR="0;33"
CPE_NAME="cpe:2.3:o:amazon:amazon_linux:2"
HOME_URL="https://amazonlinux.com/"
```

## ディスク拡張

- [Amazon EBS ボリュームのサイズ変更後にファイルシステムを拡張 - Amazon EBS](https://docs.aws.amazon.com/ja_jp/ebs/latest/userguide/recognize-expanded-volume-linux.html)
- [[AWS] EC2のストレージ(EBS)を手動で拡張する手順 (RHEL) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2025/05/09/222749)

流れは以下

1. UIやAWS CLIなどでブロックデバイスを拡張
1. `lsblk`でディスクとパーティション番号を確認し`growpart <ディスク> <パーティション番号>`でパーティション拡張 (`sudo growpart /dev/xvda 4`など)
1. `df -hT`でファイルシステムを確認し`xfs_growfs -d <マウントポイント>`でサイズ拡張
    - xfsの場合は`xfs_growfs`コマンド
    - ext4の場合は`resize2fs`コマンド

## cloud-init

### ホスト名設定

```yaml
hostname: example-host-name
```

<https://cloudinit.readthedocs.io/en/latest/reference/modules.html#set-hostname>

OSによっては`hostname`では効果がなく、その場合は`fqdn`で指定する。(RHEL9は効かなかった)

```yaml
fqdn: controller.example.org
```

### パスワード設定

```yaml
#cloud-config
password: 'p@ssword'
chpasswd: { expire: false }
```

記号を含む場合はクォートするのが無難。
`expire: false`は初回ログイン時にパスワード変更を行わない。

### sshのパスワード認証許可

```yaml
ssh_pwauth: true
```

### タイムゾーン設定

```yaml
timezone: Asia/Tokyo
```

### コマンドの実行

```yaml
runcmd:
- systemctl disable --now ...
```
