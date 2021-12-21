# コマンドメモ

## date

### 書式

### 入力を指定

- 文字列

```console
$ date -d "2021/06/25 09:00"
2021年  6月 25日 金曜日 09:00:00 JST
```

```console
$ date -d "2021/06/25 09:00" +%s
1624579200
```

- epoch

```console
$ date -d @1624579200
2021年  6月 25日 金曜日 09:00:00 JST
```

```console
$ date -d @1624579200 "+%Y-%m-%d %H-%M-%S"
2021-06-25 09-00-00
```

## man

セクション番号を指定するには

```console
$ man 5 crontab
```

など

## curl

### 名前解決オプション

```
curl --cacert ../myca_server.crt --resolve ansible.example.org:443:192.168.0.41 https://ansible.example.org/
```

`ansible.example.org`という名前で`192.168.0.41:443`へアクセスする。

## sed

```console
$ sed -i -e 's/regexp/string/' file
```

`-i`でファイル更新を行う。  
`-ie`って書くと、`-i.bak`で`file.bak`のバックアップ作成と解釈されるので分ける。

## tar

### 展開時の形式指定オプション

gzの場合はzとかは不要。  
今は`tar xf hoge.tar.gz`だけで形式は自動認識する。

### 指定ディレクトリで展開したい

```console
$ tar xf archive.tar.gz -C /tmp
```

### tarファイル内の特定ファイルを取り出したい

```console
$ tar xf archive.tar.gz hoge
```

### 特定ファイルだけを指定ディレクトリ展開

```console
$ tar xf archive.tar.gz hoge -C /tmp
```

または

```console
$ tar -C /tmp -xf archive.tar.gz hoge
```

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">まぁなんでこれ試したかってーと、-Cの説明よく読んだら「ディレクトリDIRへ移動」ってあるんで、ファイル指定より前にこれ指定したら、もしかして移動処理が先に行われてから指定ファイル取り出し処理が走るんじゃね？<br>と思ったのがたまたま当たったという</p>&mdash; z a k i 🌈 (@zaki_hmkc) <a href="https://twitter.com/zaki_hmkc/status/1306950429792583681?ref_src=twsrc%5Etfw">September 18, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## zip

[[コマンドメモ] Linuxでzip操作 - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/06/18/202546)

### ファイル一覧

```console
$ zipinfo <zip-file>
```

### 展開

`-q`付ければquietモードでファイル名が出力されない

```console
$ unzip -q <zip-file>
```

`-d`で出力先ディレクトリ指定

```console
$ unzip <zip-file> -d <dirname>
```

### 圧縮

```console
$ zip gitlab-compose.zip gitlab-compose/ -r
```

`-r`は必須

## ps

### 子プロセスツリー表示

```console
$ ps 
    PID TTY          TIME CMD
 434128 pts/2    00:00:00 bash
 456871 pts/2    00:00:00 ps
$ ps f
    PID TTY      STAT   TIME COMMAND
 434128 pts/2    Ss     0:00 /bin/bash
 456875 pts/2    R+     0:00  \_ ps f
```

### 子プロセス一覧

```console
$ ps auxf
:
:
root        1961  0.0  1.1 1104052 91304 ?       Ssl   5月12   6:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd
root      241047  0.0  0.0 438492  4124 ?        Sl    5月26   0:00  \_ /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 
root      241052  0.0  0.0 217040  5636 ?        Sl    5月26   0:00  \_ /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 8000 
root      241066  0.0  0.0 298968  5572 ?        Sl    5月26   0:00  \_ /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 
root      241073  0.0  0.0 218448  5628 ?        Sl    5月26   0:00  \_ /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 49155
:
```

```console
$ ps --ppid 1961
    PID TTY          TIME CMD
 241047 ?        00:00:00 docker-proxy
 241052 ?        00:00:00 docker-proxy
 241066 ?        00:00:00 docker-proxy
 241073 ?        00:00:00 docker-proxy
```

孫プロセスは出ない

### ヘッダ無し

```console
$ ps --ppid 1961 --no-headers
 241047 ?        00:00:00 docker-proxy
 241052 ?        00:00:00 docker-proxy
 241066 ?        00:00:00 docker-proxy
 241073 ?        00:00:00 docker-proxy
```

## NetworkManager

### デフォルトゲートウェイ変更

`192.168.39.89`に設定する

```console
# nmcli c m ens192 ipv4.gateway 192.168.39.89
```

設定削除する

```console
# nmcli c m ens192 ipv4.gateway ""
```

[[Ansible] ターゲットホストのIPアドレスの参照とデフォルトゲートウェイの設定についてのメモ - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/01/08/094833)

### スタティックルート追加

環境: [[VyOS] NAT設定を使ったネットワーク間のルーティング (手動 & Ansible / vyos_config) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/04/15/092340)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/z/zaki-hmkc/20210415/20210415085905.png)

やりたいこと

- デフォルトゲートウェイはens192側の192.168.0.1
- NICは2面 (ens192: 192.168.0.44/24, ens224: 172.16.0.44/23)
- 172.29.0.89 へ疎通を追加したい
    - このアドレスはens224の先の172.16.1.3/23がルーティングする

コマンド

```console
# nmcli c m ens224 +ipv4.routes "172.29.0.0/24 172.16.1.3"
# nmcli c u ens224
```

[4.2. nmcli を使った静的ルートの設定 Red Hat Enterprise Linux 7 | Red Hat Customer Portal](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/networking_guide/sec-configuring_static_routes_using_nmcli)

この辺が追加される。  
リブートしても有効

```console
[root@fedora-node ~]# ip r
default via 192.168.0.1 dev ens192 proto static metric 100 
:
:
172.29.0.0/24 via 172.16.1.3 dev ens224 proto static metric 101 
```

Fedora34だと設定ファイルは`/etc/NetworkManager/system-connections/ens224.nmconnection`で、内容はこんな感じ。

```
[ipv4]
address1=172.16.0.44/23
dns=172.16.2.0;
dns-search=
method=manual
route1=172.29.0.0/24,172.16.1.3
```

設定解除は`-`を付けて実行。

```console
# nmcli c m ens224 -ipv4.routes "172.29.0.0/24 172.16.1.3"
```

## firewall-cmd

[[コマンドメモ] firewall-cmd備忘録 (firewalld) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/05/26/230958)

## KVM

### 一覧

```
$ virsh list --all
```
