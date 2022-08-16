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
