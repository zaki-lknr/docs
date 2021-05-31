# proxy設定

## Docker Engineのproxy設定

[HTTP/HTTPS proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)

systemdのユニットファイルの設定に変数設定する。

```
$ sudo mkdir -p /etc/systemd/system/docker.service.d
$ cat /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:8080"
Environment="HTTPS_PROXY=http://proxy.example.com:8080"
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

これでproxy環境下にあるホストノード上で`docker pull`などできる。
