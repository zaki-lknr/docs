# ネットワーク

## Cisco

### DevNetのCSR1000v (Always-On)

[DevNet Sandbox - Lab Catalog - Dashboard](https://devnetsandbox.cisco.com/RM/Topology)  
上記から"CSR100v"で検索して見つかるもののうち"Always-On"になっているものを利用する。  
リンクを開けばOverviewの項に接続情報が記載されている。

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

### ページャーの無効化

`terminal length 0`相当の操作は以下。

```console
set cli screen-length 0
```

### シャットダウン

rootでログインして`cli`を実行。

```console
root@SRX% cli
```

その後`request system power-off`を実行する。

```console
request system power-off
Power Off the system ? [yes,no] (no) yes
```
