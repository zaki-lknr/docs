# PostgreSQL

## CLI

### バージョン確認

```console
psql --version
```

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

### 設定一覧

起動オプション(パラメタ)類はこれで確認できる。

```console
show all;
```

### 拡張一覧

```console
select * from pg_available_extensions;
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
