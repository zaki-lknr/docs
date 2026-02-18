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

特定コンテナのみ起動する場合はサービス名を指定。(複数指定可)

```console
docker compose up -d <service-name> <service-name> <service-name>
```

### ビルド

```console
docker compose build [service-name]
```

composeファイルに`build`定義があればイメージビルドを行う。

### プロジェクト名を指定

デフォルトではディレクトリ名が使用されるが、そのままだと異なるコンテナ環境でもディレクトリ名がたまたまでも同一だと競合してしまう。その場合はプロジェクト名を指定する。

```console
docker compose -p project-name up -d
docker compose -p project-name ps
```

### プロジェクトディレクトリ指定

composeファイルのあるディレクトリを指定。

```console
docker compose --project-directory /path/to/dir up -d
docker compose --project-directory /path/to/dir ps
```

デフォルトは`-f`で指定したcomposeファイルのパスなので、`-f`を使えば基本的に不要(な気がする)

### composeファイル指定

`-f`で指定する。

```console
docker compose -f /path/to/compose-v1.2.3.yml up -d
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

- [Compose file reference | Docker Docs](https://docs.docker.com/reference/compose-file)
- [Compose ファイル リファレンス ドキュメント](https://docs.docker.jp/reference/compose-file/toc.html)
- ~~[Compose file version 3 reference | Docker Documentation](https://docs.docker.com/compose/compose-file/compose-file-v3/)~~

### command

`CMD`を上書きする

```yaml
  my_container:
    image: ubuntu:latest
    command: tail -f /dev/null
```

ファイルへリダイレクトする場合は`sh -c`をかます

```yaml
  my_container:
    image: ubuntu:latest
    command: sh -c "command >> /mnt/log.txt"
```

### ホスト名

コンテナ内におけるホスト名はデフォルトではコンテナIDになる。  
ホスト名を明示するには`hostname`で指定する。

```yaml
  app:
    hostname: appserver
    image: "appserver:latest"
```

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

### 環境変数の参照

デフォルトでは`.env`ファイルの環境変数を参照する。
Composeファイルはシェルの変数参照と同じ書式で環境変数をセット(投入)できる。

```yaml
services:

  server:
    image: "rancher/k3s:${K3S_VERSION:-latest}"
    command: server
    environment:
    - K3S_TOKEN=${K3S_TOKEN:?err}
```

こんなcomposeファイルに、以下`.env`ファイルを同じディレクトリに用意しておけば、そのまま環境変数としてセットされてコンテナ起動できる。

```
K3S_VERSION=v1.33.5-k3s1
K3S_TOKEN=298143103216182
```

### コンテナ内の環境変数定義

`environment`で指定する。  
前述`.env`による設定はComposeファイル上の変数参照に使用するものであり、コンテナ内の環境変数定義は別。

### 常駐型でないワンショット処理の起動

そのまま`services`に定義すればよい。

```yaml
services:
  server:
    image: httpd

  command:
    image: fedora:latest
    command: ....
```

この場合httpdは動作し続けるが、fedoraコンテナで実行するコマンドは終了すればコンテナも停止(正常終了)する。
