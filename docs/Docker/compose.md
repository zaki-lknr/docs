# Docker Compose

## command

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
