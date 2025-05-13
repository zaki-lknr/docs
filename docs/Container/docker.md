# Docker

## dockerコマンド

[docker container / image コマンド新旧比較 - Qiita](https://qiita.com/zembutsu/items/6e1ad18f0d548ce6c266)

### run

#### -u | --user

ユーザーIDを指定して実行。  
例えばroot権限でシェル起動とか。

```console
docker run --rm -it localhost:5000/my-image sh
sh-5.1$ id
uid=1000(1000) gid=0(root) groups=0(root)
sh-5.1$ exit
exit
docker run --rm -it --user root localhost:5000/my-image sh
sh-5.1# id
uid=0(root) gid=0(root) groups=0(root)
sh-5.1# 
```

#### -p | --publish

`localでlistenするポート`:`コンテナに接続するポート`を指定する。

```console
-p 8081:80
```

これでホスト上は`8081`でListenして、その接続をコンテナの80へ転送する。

#### ボリューム (-v)

[ボリュームの利用 | Docker ドキュメント](https://matsuand.github.io/docs.docker.jp.onthefly/storage/volumes/)

ボリュームマウント

```console
$ docker run --name httpd --rm -d -v webvolume:/var/tmp httpd
```

`webvolume`ボリュームを(無ければ自動作成して)`/var/tmp`にマウントしてコンテナ起動。  
コンテナを停止・削除してもボリュームは残るので、このボリュームを別コンテナでマウントすればデータを保持できる。

#### ホストのアドレス(--add-host)

`host.docker.internal`をホストOSのアドレスに設定するには`host-gateway`を指定する。

```console
docker run --add-host host.docker.internal:host-gateway --rm -it centos bash
```

これでコンテナ内の`/etc/hosts`に`host.docker.internal`のエントリが追加され、ホストOSへのアドレスが設定される。

#### ENTRYPOINTの上書き(--entrypoint)

`CMD`でコマンド指定されたイメージであれば

```console
docker run -it image:tag sh
```

などでコンテナ内のシェルを起動できるが、`ENTRYPOINT`が使われていると起動コマンドの上書きはできない。  
その場合は`--entrypoint`オプションで上書きする。

ただしイメージ指定より手前に指定する必要がある。

```console
docker run -it --entrypoint sh image:tag
```

#### 環境変数(-e)

コンテナ環境へ環境変数を渡すには`-e`を使う

```console
docker run --rm -e APP_VAL=foobar image:tag
```

### build

#### Dockerfile指定

`-f`で指定する。

```console
docker build -f my-dockerfile . -t image:tag
```

### stats

リソース使用状況を確認。

```console
CONTAINER ID   NAME                            CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O        PIDS 
f5948afa8097   gitlab-ce-gitlab-runner-1       0.00%     10.82MiB / 7.752GiB   0.14%     2.59kB / 126B     885kB / 0B       9 
31ba18fa3004   gitlab-ce-gitlab-ce-1           1.33%     3.169GiB / 7.752GiB   40.88%    31.5kB / 1.75MB   107MB / 2.07GB   252 
```

### inspect

コンテナの状態を確認する

```console
docker inspect <container name or ID>
```

#### formatでIPアドレス

`docker run`でデフォルトのネットワークへデプロイしたコンテナであればこの通り。

```console
$ docker inspect <container name or ID> --format '{{ .NetworkSettings.IPAddress }}'
172.17.0.2
```

デフォルト以外のネットワークはこのパスの値は空になっているので、ネットワークの指定も必要。  
Compose使用時もこのパターン。

```console
$ docker inspect <container name or ID> --format '{{ .NetworkSettings.Networks.<network name>.IPAddress }}'
172.18.0.2
```

#### Logのパス

Logging Driverがjson-fileの場合

```console
docker inspect <container name or ID> --format '{{ .LogPath }}'
/var/lib/docker/containers/.../...-json.log
```

### save

```console
$ docker save imagename:tag > imagename-tag.tar
```

または

```
$ docker save imagename:tag -o imagename-tag.tar
```

### load

```console
$ docker load -i image.tar
```

あるいは

```console
$ docker load < image.tar
```

### pull

sha256ハッシュ値指定

```console
$ docker pull httpd@sha256:f2179b693cfb49baa6e7500171deea7bef755338bf165b39aedacf2b4ae28455
```

### volume

リストする。

```console
$ docker volume ls
```

参照されてないボリュームをリストアップする。  
[ダングリング](https://docs.docker.jp/engine/reference/commandline/volume_ls.html#id2)

```console
$ docker volume ls -f dangling=true
```

### image

#### サイズ順にソート

テンプレート付きのtableフォーマットでサイズをまず出力し、`sort`を`-h (--human-numeric-sort)`オプション付けてソートする。

```console
docker image ls --format "table {{.Size}}\t{{.Repository}}:{{.Tag}}\t{{.ID}}" | sort -h
```

[Docker image 一覧を容量順でソートする #Docker - Qiita](https://qiita.com/peaceiris/items/e38648a1e8614c7d5083)

### prune

不要オブジェクトを削除するには`docker prune`を使う。

#### コンテナのprune

```console
$ docker container prune
```

デフォルトは停止している全コンテナが削除される。  
10日以前に ~~停止している~~ 作成されて現在停止中のコンテナの場合は、

```console
$ docker container prune --filter "until=240h"
```

[Go duration strings](https://pkg.go.dev/time#ParseDuration)を使って期間を指定できる。  
指定可能なのは以下、かな？

> Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h"

#### イメージのprune

```console
$ docker image prune
```

デフォルトは、タグが設定されずに`<none>`になっているイメージが削除される。  
`<none>`のイメージをリストアップするだけであれば `docker image ls -f "dangling=true"` でリストアップできる。

### network

#### ls

Dockerネットワークのリスト表示

```console
$ docker network ls
NETWORK ID     NAME                        DRIVER    SCOPE
34ef60dd22b5   ansible-semaphore_default   bridge    local
69fdc4f1b801   bridge                      bridge    local
5a51254c9202   compose-sample_default      bridge    local
0ee332b7e247   guacamole-network           bridge    local
a3434bde2af6   host                        host      local
d2561ec39f0b   kind                        bridge    local
11eb1e17b151   metrics_default             bridge    local
75cd64743a98   minikube                    bridge    local
550454724fbf   none                        null      local
```

#### create

Dockerネットワークの作成

```console
$ docker network create memoapp-network
308817de4a7fd68fdf811af09ac00eceb20a20ac7b522d1b89bc16db8130d288
```

### system

#### ストレージ使用状況

コンテナ・イメージ・ボリューム・ビルドキャッシュ毎のサイズをサマライズする。

```console
$ docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          63        2         11.71GB   11.66GB (99%)
Containers      2         1         0B        0B
Local Volumes   251       1         51.49GB   51.34GB (99%)
Build Cache     111       0         770.7MB   770.7MB
```

`-v`付与で全項目ごとにサイズを出力する。

```console
$ docker system df -v
Images space usage:

REPOSITORY                                              TAG                    IMAGE ID       CREATED         SIZE      SHARED SIZE   UNIQUE SIZE   CONTAINERS
lscr.io/linuxserver/openssh-server                      latest                 8f57469f6bb9   4 weeks ago     35MB      0B            34.99MB       0
:
:
Containers space usage:

CONTAINER ID   IMAGE                                     COMMAND                   LOCAL VOLUMES   SIZE      CREATED         STATUS                     NAMES
e744ed9323d6   quay.io/prometheus/node-exporter:v1.9.0   "/bin/node_exporter …"   0               0B        8 weeks ago     Up 6 days                  node_exporter
529c27ccf88a   registry:2                                "/entrypoint.sh /etc…"   1               0B        14 months ago   Exited (2) 14 months ago   registry

Local Volumes space usage:

VOLUME NAME                                                        LINKS     SIZE
04ae90cb44468f08913852bff0427b82c2c3ef4339d3457dbbffc693900b39b3   0         17.72MB
c2c40bf44e4e08fff8d8cad68df82b849710f31c16e2a71b6c217284a218d58d   0         2.759kB
:
:

Build cache usage: 770.7MB

CACHE ID       CACHE TYPE     SIZE      CREATED         LAST USED       USAGE     SHARED
dzty9gukiwho   regular        87.7MB    16 months ago   16 months ago   1         false
p8kz9o4lxuyj   regular        115MB     16 months ago   16 months ago   1         false
:
:
```

## 実行サンプル

### 何もしないコンテナ

```console
docker run -d <image> tail -f /dev/null
```

### ローカルのwebコンテンツをhttpdに持たせる

```console
docker run --name http_sample --rm -p 8081:80 -d -v $PWD:/usr/local/apache2/htdocs httpd:latest 
```

### centos:stream8

[quayにあるイメージ](https://quay.io/repository/centos/centos?tab=tags)を使う

```console
docker run --name cent-stream --rm -it quay.io/centos/centos:stream8 bash
```

## ビルド

### ヘルスチェック

30秒後にhttpdが起動するようになっているコンテナイメージ。

```Dockerfile
FROM httpd:2.4

COPY index.html /usr/local/apache2/htdocs/
RUN apt-get update; apt-get install curl

HEALTHCHECK --interval=5s --timeout=5s --retries=15 CMD curl -f http://127.0.0.1 || exit 1

#CMD ["httpd-foreground"]
CMD ["sh", "-c", "sleep 30; httpd-foreground"]
```

起動処理中は

```console
[zaki@node slowstart_httpd]$ docker run --rm -d -p 8080:80 --name slowstart_httpd slowstart_httpd
e5c366cb3bbab10284ab41bd3e23fbf24720c9c371c35a8137d78a3c138844c6
[zaki@node slowstart_httpd]$ docker container ls
CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS                            PORTS                                   NAMES
e5c366cb3bba   slowstart_httpd   "sh -c 'sleep 30; ht…"   5 seconds ago   Up 4 seconds (health: starting)   0.0.0.0:8080->80/tcp, :::8080->80/tcp   slowstart_httpd
```

起動後は

```console
[zaki@node slowstart_httpd]$ docker container ls
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS                    PORTS                                   NAMES
e5c366cb3bba   slowstart_httpd   "sh -c 'sleep 30; ht…"   32 seconds ago   Up 31 seconds (healthy)   0.0.0.0:8080->80/tcp, :::8080->80/tcp   slowstart_httpd
```

## 設定

### insecureなレジストリ設定

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

### DNS

コンテナ内で使用するDNS設定を指定するには、`--dns`を指定するか、ホストの`/etc/docker/daemon.json`で一律指定する。

```json
{
        "dns": ["8.8.8.8", "8.8.4.4"]
}
```

### proxy設定

#### Docker Engineのproxy設定

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

### リモートからのTCP接続

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
