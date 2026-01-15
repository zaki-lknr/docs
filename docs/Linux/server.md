# サーバー関連

## samba

### ユーザー追加

ユーザー`zaki`を作成する

```console
pdbedit -a zaki
```

### 登録済みユーザー一覧

```console
pdbedit -L
```

### firewalld設定

```console
# 使用してるインタフェースがどのゾーンか確認
firewall-cmd --get-active-zones
# 対象ゾーンに許可追加
firewall-cmd --permanent --zone=FedoraServer --add-service=samba
firewall-cmd --reload
```

### SELinux設定

ホームは見えるけどアクセスできない場合

```console
setsebool -P samba_enable_home_dirs on
```

共有ディレクトリの場合

```console
semanage fcontext -a -t samba_share_t /share
restorecon -R /share/
```

## HAProxy

<https://docs.haproxy.org/2.9/configuration.html#4.2-timeout>

### タイムアウト

| 設定      | 機能                            |
| ------- | ----------------------------- |
| connect | バックエンドへの接続タイムアウト              |
| server  | バックエンドとの無応答タイムアウト             |
| client  | フロントエンド(接続元クライアント)との無応答タイムアウト |

### 外部コマンドを使った死活チェック

スクリプトはUPの場合は戻り値を0、DOWNは非0を返すように用意する。
スクリプト実行の際には、第3引数に接続先ホストのアドレス、第4引数にポート番号が渡される。

```
global
  external-check

frontend foobar
  bind *:8080
  default_backend bazqux

backend bazqux
  option external-check
  external-check path "/usr/bin:/bin"
  external-check command /path/to/script.sh
  server host1 192.168.0.121:8080 check
  server host2 192.168.0.122:8080 check
```

checkでなくbackupもある。check対象が全滅したらbackupを使う動作

### ssl検証無視

```
backend servers
  server host1 192.168.0.68:6443 ssl verify none check
```

### 統計情報

```console
echo "show stat" | socat stdio /var/lib/haproxy/stats
```

## dnsmasq

### 起動オプション

#### フォアグラウンド

`--no-daemon`を付けるとバックグラウンドへフォークしない。

### dhcp

#### IPアドレス固定

書式：`dhcp-host=hwaddr,hostname,ipaddr`

```
dhcp-host=00:00:5E:00:53:00,raclette,192.168.0.129
```

#### DHCPアドレス設定

アドレスレンジとリース時間の設定

```
dhcp-range=192.168.0.201,192.168.0.239,255.255.255.0,12h
```

#### リース状態

ファイルはデフォルトで `/var/lib/misc/dnsmasq.leases` にある。

### DNS

#### ワイルドカード

ワイルドカード的に任意の文字列をサブドメイン扱いですべて特定のアドレスに名前解決する

```
address=/exmaple.org/192.168.0.15
```

これで`example.org`だけでなく、`foo.example.org`も`foo.bar.baz.example.org`も`192.168.0.15`となる。
