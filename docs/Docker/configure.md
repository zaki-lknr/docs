# 設定

## insecureなレジストリ設定

```console
Error response from daemon: Get "https://registry.example.org/v2/": tls: failed to verify certificate: x509: certificate relies on legacy Common Name field, use SANs instead
```

ローカルに立てたレジストリとかでこんなエラーが出る場合。  
`/etc/docker/daemon.json`に、TLS検証を無視したいサイトを以下のように追加

```json
{
  "insecure-registries" : ["gitlab-ce.example.org:25000", "gitlab-ce.local:25000" ]
}
```

## DNS

コンテナ内で使用するDNS設定を指定するには、`--dns`を指定するか、ホストの`/etc/docker/daemon.json`で一律指定する。

```json
{
        "dns": ["8.8.8.8", "8.8.4.4"]
}
```

## proxy設定

### Docker Engineのproxy設定

[HTTP/HTTPS proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)

systemdのユニットファイルの設定に変数設定する。

```console
$ sudo mkdir -p /etc/systemd/system/docker.service.d
$ cat /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:8080"
Environment="HTTPS_PROXY=http://proxy.example.com:8080"
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

これでproxy環境下にあるホストノード上で`docker pull`などできる。

## リモートからのTCP接続

/lib/systemd/system/docker.service

```diff
--- /lib/systemd/system/_docker.service 2021-04-10 07:46:42.000000000 +0900
+++ /lib/systemd/system/docker.service  2021-08-10 09:39:32.451408694 +0900
@@ -10,7 +10,7 @@
 # the default is not to use systemd for cgroups because the delegate issues still
 # exists and systemd currently does not support the cgroup feature set required
 # for containers run by docker
-ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
+ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0 --containerd=/run/containerd/containerd.sock
 ExecReload=/bin/kill -s HUP $MAINPID
 TimeoutSec=0
 RestartSec=2
```

これで2375/TCPでListenする。
