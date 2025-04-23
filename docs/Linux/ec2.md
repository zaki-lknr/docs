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

## cloud-init

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
