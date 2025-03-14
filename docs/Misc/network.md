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

#### 参照

##### インタフェースの情報を見る

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

#### 設定

##### コンフィギュレーションモードへ移行

```console
ios#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
ios(config)#
```

##### ホスト名変更

```console
ios(config)#hostname router1
router1(config)#
```

##### 設定の保存

`write`で、`running-config`が`startup-config`へコピーされる。

```console
router#write
Building configuration...
[OK]
router#
```

##### ユーザー名とパスワードを設定

SSH接続するには必要。  
以下は`cisco`ユーザーにパスワード`curry_tabetai`をセット

```console
router(config)#username cisco password curry_tabetai
 WARNING: Command has been added to the configuration using a type 0 password. However, recommended to migrate to strong type-6 encryption
router(config)#
```

##### 特権パスワードの設定

`enable`実行時のパスワードを`cisco`に設定。  
SSH接続時はこれをセットしておかないと昇格できない。

```console
router(config)#enable secret cisco
router(config)#
```

##### SSH接続時の認証設定

```console
router(config)#line vty 0 4
router(config-line)#login local
```

`login local`が必要。

#### インタフェース設定

##### インタフェース設定モードへ移行

コンフィギュレーションモードからさらにインタフェースを指定

```console
router#configure terminal
router(config)#interface Ethernet0/1
router(config-if)#
```

とか

```console
router1#configure terminal
router1(config)#interface GigabitEthernet 2
```

##### IPアドレスの設定と有効化

`shutdown`されているインタフェースは、`no shutdown`で有効化される。

```console
router(config)#interface Ethernet0/2 
router(config-if)#ip address 10.0.0.1 255.255.255.0
router(config-if)#no shutdown
router(config-if)#end
router#
router#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            192.168.10.1    YES NVRAM  up                    up      
Ethernet0/1            unassigned      YES NVRAM  administratively down down    
Ethernet0/2            10.0.0.1        YES manual up                    up      
Ethernet0/3            192.168.0.35    YES manual up                    up      
router#
```

コマンド投入した時点で有効になるが、設定が保存されてるわけではないのでこのまま再起動すると設定は消える。

##### インタフェースの無効化

`shutdown`を使う。
以下はGigabitEthernet 2を無効化する場合。

```console
router1(config)#interface GigabitEthernet 2
router1(config-if)#shutdown
router1(config-if)#
```

#### その他

##### 文字列検索(grep)

`include`コマンドを使う。正規表現可

```console
router#show interfaces | include [Ee]th 
Ethernet0/0 is up, line protocol is up 
Ethernet0/1 is administratively down, line protocol is down 
Ethernet0/2 is up, line protocol is up 
Ethernet0/3 is up, line protocol is up 
router#
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
