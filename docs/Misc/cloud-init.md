# cloud-init

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
