# ネットワーク

## 用語

- VRRP
    - [VRRPとは](https://www.infraexpert.com/study/fhrpz06.html)
- アドバタイズ
    - 広告・広報するの意味
- DHCPスヌーピング
    - 対応したスイッチで「信頼できるポート」以外のDHCPトラフィックを遮断することで、不正なDHCPサーバーのなりすまし等を防ぐ

## Cisco

### CLI

#### 管理系

##### 昇格

```console
ios>enable
Password: 
rt-center#
```

`show interfaces`とか実行するには昇格する。

元のユーザーモードに戻るには`disable`を実行。

##### 再起動

```console
router1#reload 

System configuration has been modified. Save? [yes/no]: yes
Building configuration...
[OK]
Proceed with reload? [confirm]
Connection to 10.1.1.254 closed by remote host.
Connection to 10.1.1.254 closed.
```

#### インタフェースの情報を見る

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

#### コンフィギュレーションモードへ移行

```console
ios#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
ios(config)#
```

#### ホスト名変更

```console
ios(config)#hostname router1
router1(config)#
```

#### インタフェース設定モード

```console
router(config)#interface Ethernet 0/1
router(config-if)#
```

#### インタフェース設定モード

コンフィギュレーションモードからさらにインタフェースを指定

```console
router1#configure terminal
router1(config)#interface GigabitEthernet 2
```

#### インタフェースの無効化

`shutdown`を使う。元に戻すのは`no shutdown`  
以下はGigabitEthernet 2を無効化する場合。

```console
router1(config)#interface GigabitEthernet 2
router1(config-if)#shutdown
router1(config-if)#
```

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

## SmartCS

NW機器のコンソールポートへアクセスするためのコンソールサーバー。  
2通りのアクセス方法がある。

- ダイレクトモード
    - SmartCSの特定ポートへsshすると、接続しているTTYへアクセスできる
- セレクトモード
    - SmartCSの代表ポート(22/TCPのssh)へまずアクセスし、そこからTTYを選択

## VyOS

### 初期パスワード

- username: vyos
- password: vyos

### 設定モード

```console
vyos@vyos:~$ configure 
[edit]
vyos@vyos# 
```

設定を反映するには`commit`を実行。保存は`save`  
反映せずに破棄するには`discard`を実行。

設定モードを抜けるには`exit`

### リブート

saveされていない設定がなくてもなぜか警告がでる。

```console
vyos@vyos:~$ reboot 
Warning: there are unsaved configuration changes!
Run 'save' command if you do not want to lose those changes after reboot/shutdown.
Are you sure you want to reboot this system (vyos)? [y/N] y

Broadcast message from root@vyos on pts/1 (Fri 2025-02-14 07:09:44 UTC):

The system will reboot now!


Connection to vyos closed by remote host.
Connection to vyos closed.
```
