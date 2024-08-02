# Docker Compose

## command

- [Overview of docker compose CLI | Docker Documentation](https://docs.docker.com/compose/reference/)

### 起動

```console
docker compose up
```

この場合フォアグラウンドで起動する。  
バックグラウンドで起動するには`-d`を付ける。

```console
docker compose up -d
```

### 停止と削除

```console
docker compose down
```

`down`でコンテナは削除されるが、ボリュームは残る。  
ボリュームも削除するには`-v`オプションを追加する。

```console
docker compose down -v
```

特定のコンテナのみ停止・削除するには、引数にCompose Fileで指定している名称を指定する。  
ただし`down`ではこの指定はできない。

```console
docker compose stop myapp
docker compose rm myapp
```

### composeファイル指定

`-f`で指定する。

```console
docker compose -f compose-v1.2.3.yml up -d
```

### コマンド起動

`docker exec`と異なり`-it`オプションは不要。アプリ名とコマンド名のみでOK

```console
docker compose exec <app名> bash
```

### ファイルコピー

```console
docker compose cp app:/path/to/file ./file
```

## Composeファイル

- [Compose file version 3 reference | Docker Documentation](https://docs.docker.com/compose/compose-file/compose-file-v3/)

### network_mode

`"service:[service name]"`と記述すると、対象サービスのネットワーク設定を使用する。  
記述例

```yaml
services:
    app:
        image: myapp
        ports:
            - 8080:8080
        networks:
            default:
                ipv4_address: 192.168.0.100
    db:
        image: mysql
        network_mode: service:app
```

### ファイルのbindマウント

```yaml
services:

  server:
    image: "rancher/k3s:v1.30.2-k3s2"
    command: server
    :
    volumes:
    - ./registries.yaml:/etc/rancher/k3s/registries.yaml
```
