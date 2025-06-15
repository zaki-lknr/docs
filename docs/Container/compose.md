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

### restartポリシー

サービス名直下に記載する。

```yaml
services:
  prometheus:
    image: quay.io/prometheus/prometheus:v3.2.1
    :
    restart: always
```

- no: デフォルト。再起動なし
- on-failure: 終了コードがエラー時に再起動する
- always: 終了コードにかかわらず再起動する(OSリブート後など含む)
- unless-stopped: 手動停止した場合は起動しない。

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

#### 単純な依存設定 (depends_on)

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

#### ヘルスチェックとの併用 (healthcheck + condition: service_healthy)

postgresで自身にアクセスできるまで待つ設定を`healthcheck`で定義し、`depends_on`の`condition`パラメタに`service_healthy`を指定することでヘルスチェックが完了するまで待つ構成。

```yaml
    db:
        image: postgres:12
        healthcheck:
            test: ["CMD-SHELL", "pg_isready -U user || exit 1"]
            interval: 1s
            timeout: 1s
            retries: 10
            start_period: 1s
    app:
        image: app
        depends_on:
            db:
                condition: service_healthy
```

`condition`には以下が指定できる。

- `service_started`: 依存したコンテナが起動している
- `service_healthy`: 依存したコンテナのヘルスチェックが正常
- `service_completed_successfully`: 依存したコンテナが正常に終了

<https://docs.docker.jp/compose/compose-file/compose-file-v3.html#depends-on>

### マウント

#### bindマウント

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

#### volumeマウント

long syntaxだと以下

```yaml
services:
  prometheus:
    volumes:
    - type: volume
      source: prometheus-data
      target: /prometheus

volumes:
  prometheus-data: {}
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
