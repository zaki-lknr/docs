# 基本・ネタ帳

## dockerコマンド

[docker container / image コマンド新旧比較 - Qiita](https://qiita.com/zembutsu/items/6e1ad18f0d548ce6c266)

### run

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

### save

```console
$ docker save imagename:tag > imagename-tag.tar
```

または

```
$ docker save imagename:tag -o imagename-tag.tar
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

## GitHub Container Registry

手順は以下。  
[コンテナレジストリの利用 - GitHub Docs](https://docs.github.com/ja/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

認証に使用するパスワードは、個人用アクセストークンを作成する。  
[個人アクセストークンを使用する - GitHub Docs](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

GitHubのユーザー名と、入手したトークンをパスワードとして、

```console
$ docker login ghcr.io
```

すれば認証できる。

あとは`ghcr.io/zaki-lknr/squid:5.0.4-alpine-3.13`とかの名前のタグをつければ`push`もできる。  
pushされたイメージは、GitHubのweb画面の「Packages」で確認できる。デフォルトはprivate設定。
