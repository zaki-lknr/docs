# cloud-init

## アカウント

ユーザー名とパスワード

```yaml
user: yutakasaki
password: hitoridakenanteerabenaiyo
```

## SSH

### パスワード認証

`true`で許可

```yaml
ssh_pwauth: true
```

## パッケージインストール

```yaml
packages:
- podman
- qemu-guest-agent
```
