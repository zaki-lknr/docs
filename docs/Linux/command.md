# コマンドメモ

## less

### 制御文字を認識させる

`ESC[32m`とか大量に表示されたときは、表示中に`-r`押下して`enter`

### 大文字小文字無視して検索

表示中に`-i`押下して`enter`

## find

### ディレクトリ除外

```console
$ find /path/to -path (除外パス) -prune -o (検索条件) -print
```

```console
find . -path "*venv" -prune -o -name "*.py" -print
```

とか。

### スペース込みのパスとxargsの連携

以下で、findの結果が\0区切りになり、xargsのデリミタの扱いが\0になる。

```console
$ find path [opt] -print0 | xargs -0 command
```

`find`は結果を改行区切りで出力し、`xargs`は改行とスペースを区切りとみなすのがデフォルト。  
`find`に`-print0`を付与すると区切りが`\0`に変更され、`xargs`に`-0`を付与すると`\0`区切りで動作する。

```console
zaki@salva% find . -name "*txt"
./a b.txt
./a.txt
./b.txt
./sub/a b.txt
./sub/a.txt
./sub/b.txt
```

```console
zaki@salva% find . -name "*txt" | od -c
0000000   .   /   a       b   .   t   x   t  \n   .   /   a   .   t   x
0000020   t  \n   .   /   b   .   t   x   t  \n   .   /   s   u   b   /
0000040   a       b   .   t   x   t  \n   .   /   s   u   b   /   a   .
0000060   t   x   t  \n   .   /   s   u   b   /   b   .   t   x   t  \n
```

```console
zaki@salva% find . -name "*txt" -print0 | od -c
0000000   .   /   a       b   .   t   x   t  \0   .   /   a   .   t   x
0000020   t  \0   .   /   b   .   t   x   t  \0   .   /   s   u   b   /
0000040   a       b   .   t   x   t  \0   .   /   s   u   b   /   a   .
0000060   t   x   t  \0   .   /   s   u   b   /   b   .   t   x   t  \0
```

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

## awk

`ps aux`の結果の3カラム目を抜き出す。

```console
$ ps aux | awk '{print $3}'
```

3カラム目と4カラム目をタブで区切って出力。

```console
$ ps aux | awk '{print $3, "\t", $4}'
```

または、OFS変数(output field separator)にタブをセットし以下。

```console
$ ps aux | awk 'BEGIN { OFS="\t" } {print $3, $4}'
```

## diff

コマンド実行結果のstdout同士を比較したい場合は以下。

```console
$ diff -u <(command1) <(command2)
```

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

## top

### ソート

実行中に`P`でCPU使用率、`M`でメモリ使用率でソートされる。

分かりづらいけど`x`押下でソート基準のカラムが太字になる。

### CPUコア毎

実行中に`1`押下

```console
top - 09:18:32 up 69 days, 11:39,  0 users,  load average: 0.01, 0.02, 0.05
Tasks: 134 total,   1 running, 133 sleeping,   0 stopped,   0 zombie
%Cpu0  :  2.0 us,  0.7 sy,  0.0 ni, 97.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu1  :  0.3 us,  0.7 sy,  0.0 ni, 99.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu2  :  1.3 us,  0.7 sy,  0.0 ni, 98.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu3  :  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  7990048 total,   717480 free,  1974248 used,  5298320 buff/cache
KiB Swap:  3670012 total,  3670012 free,        0 used.  5665228 avail Mem 
```

### コマンドフルパス・引数表示

実行中に`c`押下

```console
top - 09:20:43 up 69 days, 11:41,  0 users,  load average: 0.00, 0.01, 0.05
Tasks: 134 total,   1 running, 133 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.0 sy,  0.0 ni, 99.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  7990048 total,   715400 free,  1976168 used,  5298480 buff/cache
KiB Swap:  3670012 total,  3670012 free,        0 used.  5663308 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                             
 351149 zaki      20   0 1280996 358920   9912 S   0.0  4.5   8:37.98 /home/zaki/.vscode-server/data/User/globalStorage/hashicorp.terraform/bin/terraform-ls serve                        
 350657 zaki      20   0 1029428 147820  21832 S   0.3  1.9   2:04.98 /home/zaki/.vscode-server/bin/899d46d82c4c95423fb7e10e68eba52050e30ba3/node /home/zaki/.vscode-server/bin/899d46d8+ 
 350535 zaki      20   0  963792 102092  18036 S   0.0  1.3   2:00.07 /home/zaki/.vscode-server/bin/899d46d82c4c95423fb7e10e68eba52050e30ba3/node /home/zaki/.vscode-server/bin/899d46d8+ 
 385441 root      20   0 1130612  62204  26536 S   0.0  0.8   0:10.18 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock      
```

## dig

デフォルトは実行ホストのOSで設定されているネームサーバーに対してAレコードを問い合わせる。

```console
$ dig gmail.com

; <<>> DiG 9.16.28-RH <<>> gmail.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 20838
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;gmail.com.                     IN      A

;; ANSWER SECTION:
gmail.com.              180     IN      A       142.251.42.197

;; Query time: 7 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu Apr 28 10:31:34 JST 2022
;; MSG SIZE  rcvd: 54
```

## NetworkManager

### インタフェース一覧

接続してるもの全部

```console
$ nmcli connection
```

デバイス一覧

```console
$ nmcli device
```

`connection`と`device`は、それぞれ`c`と`d`に短縮可能。

### インタフェース詳細

`s`または`show`の引数に、インタフェース名を指定する。

```console
# nmcli c s ens192
connection.id:                          ens192
connection.uuid:                        fd81b589-db4b-445d-9e60-22524aab4e9f
connection.stable-id:                   --
connection.type:                        802-3-ethernet
connection.interface-name:              ens192
connection.autoconnect:                 はい
connection.autoconnect-priority:        0
connection.autoconnect-retries:         -1 (default)
connection.multi-connect:               0 (default)
:
:
```

### DNSのsearch行追加

`my.example.org`を設定するには

```console
# nmcli c m ens192 ipv4.dns-search my.example.org
# systemctl restart NetworkManager
```

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

## ファイアウォール

### iptables

ルール確認

```console
# iptables -L
```

TCPポート許可追加

```console
# iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
```

リダイレクト  
10.0.0.1からの80/tcpアクセスを8080/tcpへリダイレクトする場合。(8080/tcpも許可は必要)

```console
# iptables -t nat -A PREROUTING -p tcp -s 10.0.0.1 --dport 80 -j REDIRECT --to 8080
```

自分への443/tcpアクセスをリモートの172.29.0.14:443へリダイレクトする場合。

```console
iptables -t nat -A PREROUTING -p tcp --dport 443 -j DNAT --to-destination 172.29.0.14:443
```

### firewall-cmd

[[コマンドメモ] firewall-cmd備忘録 (firewalld) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/05/26/230958)

### ufw

現在の状態。  
※アクティブになっていない

```console
root@oci-g-a1-ubuntu:~# ufw status
Status: inactive
```

有効化する。  
ssh経由の場合警告される。(`iptables -nL`あたりで22/tcpが開いてる事を確認しておけば…良いのかな)

```console
root@oci-g-a1-ubuntu:~# ufw enable 
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```

再読み込みと状態確認。

```console
root@oci-g-a1-ubuntu:~# ufw reload 
Firewall reloaded
root@oci-g-a1-ubuntu:~# ufw status
Status: active
```

TCPポートの許可

```console
root@oci-g-a1-ubuntu:~# ufw allow 6443
Rule added
Rule added (v6)
root@oci-g-a1-ubuntu:~# ufw reload 
Firewall reloaded
root@oci-g-a1-ubuntu:~# ufw status
Status: active

To                         Action      From
--                         ------      ----
6443                       ALLOW       Anywhere                  
6443 (v6)                  ALLOW       Anywhere (v6)             
```

※ 操作のみ。この環境はufwを使ってないらしく動作は未確認

## alternatives

ubuntuだと`update-alternatives`で、RHEL系だと`alternatives`になる。

### 設定一覧

ubuntuの場合

```console
root@oci-ap-a1-ubuntu:~# update-alternatives --get-selections 
arptables                      auto     /usr/sbin/arptables-nft
awk                            auto     /usr/bin/gawk
builtins.7.gz                  auto     /usr/share/man/man7/bash-builtins.7.gz
c++                            auto     /usr/bin/g++
:
:
```

Fedoraの場合

```
[root@cloud-dev2 ~]# update-alternatives --list 
libnssckbi.so.x86_64    auto    /usr/lib64/pkcs11/p11-kit-trust.so
soelim                  auto    /usr/bin/soelim.groff
iptables                auto    /usr/sbin/iptables-nft
:
```

### 設定の登録

`python`という名前で、`/usr/bin/python`を優先度10で登録する。

```console
$ sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 10
update-alternatives: using /usr/bin/python3 to provide /usr/bin/python (python) in auto mode
```

これで、以下のリンクが作成される。

```console
$ ls -l /usr/bin/python /etc/alternatives/python 
lrwxrwxrwx 1 root root 16 Mar 31 15:16 /etc/alternatives/python -> /usr/bin/python3
lrwxrwxrwx 1 root root 24 Mar 31 15:16 /usr/bin/python -> /etc/alternatives/python
```

設定

```console
$ update-alternatives --display python
python - auto mode
  link best version is /usr/bin/python3
  link currently points to /usr/bin/python3
  link python is /usr/bin/python
/usr/bin/python3 - priority 10
```
