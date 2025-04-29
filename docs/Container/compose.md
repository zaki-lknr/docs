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

### プロジェクト名を指定

デフォルトではディレクトリ名が使用されるが、そのままだと異なるコンテナ環境でもディレクトリ名がたまたまでも同一だと競合してしまう。その場合はプロジェクト名を指定する。

```console
docker compose -p project-name up -d
docker compose -p project-name ps

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

### 依存

#### 単純な依存設定

depends_onで依存を設定することで起動・終了の順序を制御できる。

```yaml
services:
    db:
        image: postgres
    app:
        image: app
        depends_on:
            - db
```

順序が制御できるのみなので、コンテナ内プロセスの状態までは感知しない。

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

long syntaxだと以下

```yaml
    volumes:
    - type: bind
      source: ./registries.yaml
      target: /etc/rancher/k3s/registries.yaml
```

### リソース制限

OSのリソースを使いつくさないようにCPUとメモリを制限

```yaml
services:

  apps:
    image: ...
    command: ...

    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512m
```

[[Docker] コンテナで動かすプロセスのCPU使用率を--cpusで、メモリ使用量を--memoryで制限する - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/09/08/213857)

### ログファイルサイズ制限

__デフォルト無制限のためストレージを使い切ってしまうので制限する__

```yaml
services:

  apps:
    image: ...
    command: ...

    logging:
      driver: json-file
      options:
        max-size: 10m
        max-file: 3
```

[[Docker]コンテナ実行時にlog-optsでログサイズ上限とローテート数を設定してホストのストレージが溢れないようにする - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/09/09/211117)
