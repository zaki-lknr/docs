# ネットワーク

## 用語

- VRRP
    - [VRRPとは](https://www.infraexpert.com/study/fhrpz06.html)

## Cisco

### 昇格

```console
ios>enable
Password: 
rt-center#
```

`show interfaces`とか実行するには昇格する。

### インタフェースの情報を見る

```console
show interfaces
```

とか、

```console
ios# show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       10.1.1.254      YES DHCP   up                    up      
GigabitEthernet2       10.1.2.254      YES DHCP   up                    up      
Loopback0              10.255.255.1    YES TFTP   up                    up      
VirtualPortGroup0      192.168.35.101  YES TFTP   up                    up     
```

とか。

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
