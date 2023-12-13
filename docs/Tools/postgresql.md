# PostgreSQL

## CLI

### DBに接続

```console
$ psql -d <DB名> -U <ユーザー名>
```

### SQL実行時のページャを無効にするには

環境変数`PAGER`に`cat`をセットする

```console
$ export PAGER=cat
$ psql -d <DB名> -U <ユーザー名>
```

### DB一覧

```console
\l
```

### テーブル一覧

```console
\dt
```

### テーブル定義

```console
\d <table-name>
```
