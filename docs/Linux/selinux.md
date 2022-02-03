# SELinux

## port追加

### エラーログ

webサーバーで適当に`8081`でListenさせようとしてもバインドできずに起動できない。

error_log

```
[suexec:notice] [pid 1237:tid 1237] AH01232: suEXEC mechanism enabled (wrapper: /usr/sbin/suexec)
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 192.168.0.44. Set the 'ServerName' directive globally to suppress this message
[Tue Jul 06 10:47:18.353422 2021] [lbmethod_heartbeat:notice] [pid 1237:tid 1237] AH02282: No slotmem from mod_heartmonitor
```

message

```
setroubleshoot[1462]: SELinux により、httpd による name_bind アクセスが、tcp_socket ポート 8081 で拒否されました。#012#012*****  プラグイン catchall (100. 信頼性) による示唆****************************************#012#012httpd に、 ポート 8081 tcp_socket の name_bind アクセスがデフォルトで許可されるべきと考える場合。#012このようにします: バグとして報告してください。 #012ローカルのポリシーモジュールを生成すると、#012 このアクセスを許可することができます。#012そして、以下を実行します: #012以下を実行して、このアクセスを許可します:#012# ausearch -c 'httpd' --raw | audit2allow -M my-httpd#012# semodule -X 300 -i my-httpd.pp#012
```

### ポート状態

```console
[root@fedora-node ~]# semanage port -l | grep -w "http_port_t"
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
```

なるほど、8081は無い。

### 追加

```console
[root@fedora-node ~]# semanage port -a -t http_port_t -p tcp 8081
ValueError: ポート tcp/8081 はすでに定義されています
[root@fedora-node ~]# semanage port -l | grep -w 8081
transproxy_port_t              tcp      8081
```

あー、別で使ってるのか。なるほど。

```console
[root@fedora-node ~]# semanage port -l | grep -w 808.
http_cache_port_t              tcp      8080, 8118, 8123, 10001-10010
luci_port_t                    tcp      8084
transproxy_port_t              tcp      8081
us_cli_port_t                  tcp      8082, 8083
us_cli_port_t                  udp      8082, 8083
```

8085空いてる

```console
[root@fedora-node ~]# semanage port -a -t http_port_t -p tcp 8085
[root@fedora-node ~]# semanage port -l | grep -w "http_port_t"
http_port_t                    tcp      8085, 80, 81, 443, 488, 8008, 8009, 8443, 9000
```

うむ

```console
[root@fedora-node ~]# vi /etc/httpd/conf/httpd.conf 
[root@fedora-node ~]# systemctl restart httpd
[root@fedora-node ~]# curl http://localhost:8085 -I
HTTP/1.1 403 Forbidden
Date: Tue, 06 Jul 2021 01:55:15 GMT
Server: Apache/2.4.48 (Fedora)
Last-Modified: Fri, 26 Mar 2021 17:49:58 GMT
ETag: "211a-5be742910bd80"
Accept-Ranges: bytes
Content-Length: 8474
Content-Type: text/html; charset=UTF-8

[root@fedora-node ~]# 
```

はい。

### 更新

`-a`だと他で使ってるとエラーになる。  
`-m`なら更新され、他で使ってるものと多重に定義できる。

```console
[root@fedora-node ~]# semanage port -a -t http_port_t -p tcp 8082
ValueError: ポート tcp/8082 はすでに定義されています
[root@fedora-node ~]# semanage port -l | grep -w 8082
us_cli_port_t                  tcp      8082, 8083
us_cli_port_t                  udp      8082, 8083
[root@fedora-node ~]# semanage port -m -t http_port_t -p tcp 8082
[root@fedora-node ~]# semanage port -l | grep -w 8082
http_port_t                    tcp      8082, 8081, 8087, 8086, 8085, 80, 81, 443, 488, 8008, 8009, 8443, 9000
us_cli_port_t                  tcp      8082, 8083
us_cli_port_t                  udp      8082, 8083
```

```
[root@fedora-node ~]# systemctl restart httpd
[root@fedora-node ~]# curl http://localhost:8082 -I
HTTP/1.1 403 Forbidden
Date: Tue, 06 Jul 2021 04:06:38 GMT
Server: Apache/2.4.48 (Fedora)
Last-Modified: Fri, 26 Mar 2021 17:49:58 GMT
ETag: "211a-5be742910bd80"
Accept-Ranges: bytes
Content-Length: 8474
Content-Type: text/html; charset=UTF-8

[root@fedora-node ~]# 
```

[sshdの場合はこちら](https://zaki-lknr.github.io/Linux/ssh/#sshd)
