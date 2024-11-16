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

### build

#### Dockerfile指定

`-f`で指定する。

```console
docker build -f my-dockerfile . -t image:tag
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

## 実行サンプル

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
