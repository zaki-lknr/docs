# ネットワーク

## Junos

### rootのパスワードを設定する

`set system root-authentication plain-text-password`を実行する。

```console
[edit]
root#

[edit]
root# set system root-authentication plain-text-password
New password:
Retype new password:

[edit]
root#
```

### ホスト名の設定

```console
[edit]
root# set system host-name srx100-demo

[edit]
root# commit

[edit]
root@srx100-demo#
```
