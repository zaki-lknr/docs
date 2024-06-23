# EC2

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

### コマンドの実行

```yaml
runcmd:
- systemctl disable --now ...
```
