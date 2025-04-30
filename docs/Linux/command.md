# コマンドメモ

## bash

### 除外のglob

```console
ls !(*.txt)
```

`*.txt`以外を展開

### while無限ループワンライナー

```console
while :; do date; sleep1; done
```

### forループワンライナー

```console
for i in `seq 1 3`; do curl 192.168.0.16; done
```

`seq`の値を参照

```console
for i in `seq 1 3`; do curl 192.168.0.${i}; done
```

### if/elseワンライナー

```console
if [ $value == "fobar" ]; then command_a; else commandb; fi
```

## less

### 終了時にクリアしない

起動時に`-X`オプションを付加する

```console
less -X
```

### 制御文字を認識させる

`ESC[32m`とか大量に表示されたときは、表示中に`-r`押下して`enter`

### 大文字小文字無視して検索

表示中に`-i`押下して`enter`

### 行番号表示

実行時のオプションで`-N`を付与するか、表示中に`-N`押下する。

```console
less -N
```

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

## grep

### マッチした行番号表示

```console
grep -n pattern
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
curl --resolve ansible.example.org:443:192.168.0.41 https://ansible.example.org/
```

`ansible.example.org`という名前で`192.168.0.41:443`へアクセスする。

### basic認証

```command
curl -u username:password http://example.org/
```

### HTTP 0.9

```console
$ curl http://old.example.org/
curl: (1) Received HTTP/0.9 when not allowed
```

HTTP/0.9のレスポンスは`curl`はデフォルトで処理しないが、`--http0.9`をオプションに加えるとOK

### ステータスコードのみ取得

```console
curl http://www.example.org/ -w '%{http_code}\n' -sS -o /dev/null
```

### レスポンスヘッダ

```console
curl -I http://www.exmaple.org
```

### タイムアウト設定

```console
$ curl --connect-timeout 2 http://192.168.0.249
curl: (28) Failed to connect to 192.168.0.249 port 80 after 2000 ms: Timeout was reached
```

## wget

リソースの内容をstdoutへ出力

```console
$ wget -q -O - https://storage.googleapis.com/kubernetes-release/release/stable.txt
v1.24.3
```

## base64

デコード

```console
# stdinから
command | base64 -d
# ファイルから
base64 -d FILENAME
```

## sed

```console
$ sed -i -e 's/regexp/string/' file
```

`-i`でファイル更新を行う。  
`-ie`って書くと、`-i.bak`で`file.bak`のバックアップ作成と解釈されるので分ける。

### デリミタ変更

普通に使えばOK

```console
sed -e 's#foo#bar#' file
```

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

### if文

1カラム目が`root`の場合の11カラム目を出力

```console
ps aux | awk '{if($1 == "root"){print $11}}'
```

### if文で正規表現

1カラム目が数字のみの場合の行全部

```console
ps aux | awk '{if($1 ~ /[0-9]+/){print $0}}'
```

## touch

### タイムスタンプ変更

ファイル名のみ指定すればコマンド実行時のタイムスタンプに更新

```console
touch FILENAME
```

`-t`を使ってFILENAMEのタイムスタンプを2025-01-01 23:59:00に更新する。

```console
touch -t 202501012359 FILENAME
```

## ヒアドキュメント

```console
cat > /path/to/file <<__EOL__
てきすと
__EOL__
```

root権限が必要で`sudo`と組み合わせる場合であればリダイレクトでなく`tee`を使うと簡単

```console
cat | sudo tee /etc/file <<__EOL__
config: foobar
__EOL__
```

## envsubst

```console
$ cat sample.template 
hello ${USER}
```

環境変数`$USER`が定義されてる状態で、stdinから↑の内容を入力にあたえると

```console
$ envsubst < sample.template 
hello zaki
```

変数名の書式で記述した箇所が環境変数で展開される。

## diff

コマンド実行結果のstdout同士を比較したい場合は以下。

```console
$ diff -u <(command1) <(command2)
```

## screen

### マウススクロールで表示をスクロールする

`.screenrc`に以下追加

```console
termcapinfo xterm* ti@:te@
```

## tmux

### 起動とセッション

新規セッション作成

```console
tmux
```

既存セッションにアタッチ

```console
tmux a
```

セッション一覧

```console
tmux ls
```

### 設定

#### マウスホイールでスクロール

デフォルトだとコマンド履歴表示になってる

```text
set-option -g mouse on
```

#### キーバインド変更

デフォルトの`Ctrl-b`はshellのbackを上書きしているので、`Ctrl-g`に変更する。

```text
set-option -g prefix C-g
unbind-key C-b
bind-key C-g send-prefix
```

## gzip

### .gzファイルを展開してstdoutへ出力

gz圧縮されたログファイルの中身を見たいときなど

```console
gzip -dc /var/log/syslog.10.gz | less
```

## tar

### ファイルの追加

既存のtarファイルへ新たにファイルを追加する(append)

```console
tar rvf archive.tar.gz file.txt
```

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
unzip -l <zip-file>
```

あるいは

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

zip内のファイルパスを指定すれば、そのファイルのみを取り出せる。

```console
unzip <zip-file> path/to1/file1.yaml path/to2/file2.conf
```

ワイルドカードも指定可能

```console
unzip <zip-file> "path/*"
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

## kill

### kill -0

> If sig is 0, then no signal is sent, but existence and permission checks are still performed; this can be used to check for the existence of a process ID or process group ID that the caller is permitted to signal.
>
> sig に 0 を指定した場合、シグナルは送られないが、 プロセスの存在と権限のチェックは行われる。これを使って、送信者がシグナルを送信できるプロセス ID や プロセスグループ ID の存在確認を行うことができる。
>
> man kill(2)

[linux - kill -0 <PID> は何をするのでしょうか？ - スタック・オーバーフロー](https://ja.stackoverflow.com/questions/53077/kill-0-pid-%E3%81%AF%E4%BD%95%E3%82%92%E3%81%99%E3%82%8B%E3%81%AE%E3%81%A7%E3%81%97%E3%82%87%E3%81%86%E3%81%8B)

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

## ユーザー作成

### 作成する

```console
useradd zaki -m -d /home/zaki -s /bin/bash -G admin,sudo
```

| オプション | 動作             |
| ----- | -------------- |
| `-m`  | ホームディレクトリ作成を有効 |
| `-d`  | ホームディレクトリのパス   |
| `-s`  | デフォルトシェル指定     |
| `-G`  | 追加の所属グループ指定    |

### 削除する

```console
userdel zaki -r
```

| オプション | 動作                 |
| ----- | ------------------ |
| `-r`  | ホームディレクトリなどのデータを削除 |

## グループ作成

### 作成する

```console
groupadd nijigasaki [-g GID]
```

## timedatectl

### NTPの無効・有効

```console
timedatectl set-ntp false
```

`true`で有効

### タイムゾーン設定

```console
timedatectl set-timezone Asia/Tokyo
```

## dig

### Aレコード

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

### 逆引き

`-x`オプションを使う

```console
$ dig -x 192.168.0.16

; <<>> DiG 9.16.33-RH <<>> -x 192.168.0.16
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 3719
;; flags: qr aa rd ra ad; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;16.0.168.192.in-addr.arpa.     IN      PTR

;; ANSWER SECTION:
16.0.168.192.in-addr.arpa. 0    IN      PTR     cloud-dev2.
16.0.168.192.in-addr.arpa. 0    IN      PTR     cloud-dev2.local.

;; Query time: 2 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu Dec 15 17:03:57 JST 2022
;; MSG SIZE  rcvd: 108
```

## nc

### 疎通確認

102.168.0.100の22/TCPへの疎通確認 (`-v`はverboseオプション)

```console
$ nc -v 192.168.0.100 22
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 192.168.0.4:22.
SSH-2.0-OpenSSH_9.2p1 Debian-2+deb12u3

```

サーバーがクライアントからの送信待ちの場合は接続が継続する。  
疎通を確認して即切断でよければ、`-z`を付与する。  
(Zero-I/O mode, report connection status only)

```console
$ nc -z -v 192.168.0.100 22
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 192.168.0.4:22.
Ncat: 0 bytes sent, 0 bytes received in 0.01 seconds.
```

疎通がなければ失敗する。

```console
### ホストが存在しない
$ nc -z -v 192.168.0.100 22
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: No route to host.
```

```console
### ホストは存在するがポートがlistenしてない
$ nc -z -v 192.168.0.99 22
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connection refused.
```

### HTTPをしゃべる

`-v`のみで接続した、クライアントからの送信待ちの状態になったらそのまま入力すればOK

```console
$ nc -v 192.168.0.1 80
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 192.168.0.1:80.              # <- ここでサーバーが入力待ち
GET / HTTP/1.1                                  # <- キーボードからHTTPの内容を入力
Host: 192.168.0.1                               # <- 2行目
                                                # <- 空行送信でHTTPとしてクライアントからのsend完了
HTTP/1.1 200 OK                                 # <- 以降はサーバーのレスポンス
Server: nginx/1.20.1
Date: Sat, 08 Feb 2025 14:38:03 GMT
Content-Type: text/html
Content-Length: 2602
```

## ip

### リンク状態のリスト

`link`を引数に指定

```console
ip link
```

`l`のみでも同様の結果

### 指定インタフェースのみ

`show`も付与する。

```console
ip a s ens18
```

`a`は`address`の、`s`は`show`の省略形。上記は`ip address show ens18`が正しい表記。

### ルーティングテーブル確認

```console
ip route
```

`ip r`も可

### スタティックルート追加

「`192.168.0.0/24`宛のパケットは、`enp4s0`インタフェース経由で`172.16.1.0`へルーティングする」場合

```console
ip route add 192.168.0.0/24 via 172.16.1.0 dev enp4s0
```

### デフォルトルート追加

```console
ip route add default via 172.16.1.0 dev eth1 metric 1000
```

### デフォルトルート削除

```console
ip route delete default dev eth0
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

### 名称変更

デフォルトの`有線接続 1`を`ens192`とかに変えたい場合

```console
[zaki@fedora-node ~]$ nmcli c s
NAME             UUID                                  TYPE      DEVICE
有線接続 1       e8d83bb5-ce0a-3fe8-911e-8bc57153091c  ethernet  ens18
[zaki@fedora-node ~]$ sudo nmcli c m "有線接続 1" connection.id ens18
[zaki@fedora-node ~]$ nmcli c s
NAME             UUID                                  TYPE      DEVICE
ens18            e8d83bb5-ce0a-3fe8-911e-8bc57153091c  ethernet  ens18
```

### インタフェースの接続と切断

```console
# nmcli con up ens224
```

```console
# nmcli con down ens224
```

### インタフェースの削除

```console
nmcli c delete ens224
```

### IPアドレスをstaticに変更

DHCPでインストールしたあとに変更したいときとか。  
methodをmanualに変更するのはaddressesをセットしたあとでないとエラーになるので注意。

```console
nmcli c m ens192 ipv4.addresses "192.168.0.79/24"
nmcli c m ens192 ipv4.gateway "192.168.0.1"
nmcli c m ens192 ipv4.method manual
```

最後に再起動

```console
nmcli c down ens192
nmcli c up ens192
```

### DNSの設定変更

```console
# nmcli c m ens192 ipv4.dns 192.168.0.11
```

これで設定ファイルが更新されるので、`systemctl restart NetworkManager`すれば設定が反映される。

### DNSのセカンダリ追加

追加する項目である`ipv4.dns`の手前に`+`を付与すると「追加」になる。  
`-`を付与すると対象を削除になる。

`DNS1`の設定がある状態で下記を実行すると、`DNS2`の設定として追加される。

```console
# nmcli c m "System ens192" +ipv4.dns 192.168.0.1
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

## systemd

### 一覧

```console
systemctl list-unit-files
```

### ユーザー権限のユニットファイル一覧

```console
systemctl list-unit-files --user
```

## ホスト名

### 変更

```console
sudo hostnamectl set-hostname rhel8
```

## resolvectl

Fedoraなど。

### DNS設定確認

```console
resolvectl status
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

#### ルール追加

```console
iptables -A INPUT ...
```

`-A`は`--append`で末尾に追加

```console
iptables -I INPUT [n] ...
```

`-I`は`--insert`で先頭に追加。nに数値を指定すれば、その位置に挿入。

#### ルールの削除

まずルールの状態を`--line-numbers`を付与して行番号を確認。(目視でも良い)

```console
# iptables -nL --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
4    ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0            udp spt:123
5    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:80
6    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:443
7    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
8    REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited
9    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80
10   ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:443
11   ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:443
12   ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:80
```

行番号が分かったら、番号を指定して`-D`でルールと行番号指定で削除

```console
iptables -D INPUT 12
```

リダイレクト(nat)の場合も同様

```console
# iptables -L -t nat --line-numbers
```

番号を確認したら、それを引数に以下を実行。(`PREROUTING`ルールの場合)

```console
# iptables -t nat -D PREROUTING 1
```

### firewall-cmd

[[コマンドメモ] firewall-cmd備忘録 (firewalld) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/05/26/230958)

#### 確認

ゾーンの設定確認

```console
firewall-cmd --list-all [--zone=internal]
```

全ゾーンの設定確認

```console
firewall-cmd --list-all-zones
```

ゾーン一覧

```console
firewall-cmd --get-zones
```

アクティブなゾーンの一覧

```console
firewall-cmd --get-active-zones
```

#### ゾーン設定

ゾーンに指定インタフェースを設定

```console
firewall-cmd --permanent --zone=trusted --change-interface=cni0
```

ゾーンから指定インタフェースを削除

```console
firewall-cmd --permanent --zone=trusted --remove-interface=cni0
```

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

## lscpu

CPU情報の出力

```console
# lscpu 
Architecture:             x86_64
  CPU op-mode(s):         32-bit, 64-bit
  Address sizes:          39 bits physical, 48 bits virtual
  Byte Order:             Little Endian
CPU(s):                   16
  On-line CPU(s) list:    0-15
Vendor ID:                GenuineIntel
  BIOS Vendor ID:         Intel(R) Corporation
  Model name:             12th Gen Intel(R) Core(TM) i7-12650H
    BIOS Model name:      12th Gen Intel(R) Core(TM) i7-12650H To Be Filled By O.E.M. CPU @ 2.1GHz
    BIOS CPU family:      198
    CPU family:           6
    Model:                154
    Thread(s) per core:   2
    Core(s) per socket:   10
    Socket(s):            1
    Stepping:             3
    CPU(s) scaling MHz:   42%
    CPU max MHz:          4700.0000
    CPU min MHz:          400.0000
    BogoMIPS:             5376.00
    Flags:                fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi
                           mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfm
                          on pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclm
                          ulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 ss
                          e4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowp
                          refetch cpuid_fault epb cat_l2 cdp_l2 ssbd ibrs ibpb stibp ibrs_enhanced tpr_shadow flex
                          priority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid rdt_a rdse
                          ed adx smap clflushopt clwb intel_pt sha_ni xsaveopt xsavec xgetbv1 xsaves split_lock_de
                          tect user_shstk avx_vnni dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp h
                          wp_pkg_req hfi vnmi umip pku ospke waitpkg gfni vaes vpclmulqdq rdpid movdiri movdir64b 
                          fsrm md_clear serialize arch_lbr ibt flush_l1d arch_capabilities
Virtualization features:  
  Virtualization:         VT-x
Caches (sum of all):      
  L1d:                    416 KiB (10 instances)
  L1i:                    448 KiB (10 instances)
  L2:                     9.5 MiB (7 instances)
  L3:                     24 MiB (1 instance)
NUMA:                     
  NUMA node(s):           1
  NUMA node0 CPU(s):      0-15
Vulnerabilities:          
  Gather data sampling:   Not affected
  Itlb multihit:          Not affected
  L1tf:                   Not affected
  Mds:                    Not affected
  Meltdown:               Not affected
  Mmio stale data:        Not affected
  Reg file data sampling: Vulnerable: No microcode
  Retbleed:               Not affected
  Spec rstack overflow:   Not affected
  Spec store bypass:      Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:             Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:             Mitigation; Enhanced / Automatic IBRS; IBPB conditional; RSB filling; PBRSB-eIBRS SW seq
                          uence; BHI SW loop, KVM SW loop
  Srbds:                  Not affected
  Tsx async abort:        Not affected
```

## ethtool

NIC状態の出力

```console
# ethtool enp2s0
Settings for enp2s0:
        Supported ports: [ TP ]
        Supported link modes:   10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
                                2500baseT/Full
        Supported pause frame use: Symmetric
        Supports auto-negotiation: Yes
        Supported FEC modes: Not reported
        Advertised link modes:  10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
                                2500baseT/Full
        Advertised pause frame use: Symmetric
        Advertised auto-negotiation: Yes
        Advertised FEC modes: Not reported
        Speed: 2500Mb/s
        Duplex: Full
        Auto-negotiation: on
        Port: Twisted Pair
        PHYAD: 0
        Transceiver: internal
        MDI-X: off (auto)
        Supports Wake-on: pumbg
        Wake-on: g
        Current message level: 0x00000007 (7)
                               drv probe link
        Link detected: yes
```
