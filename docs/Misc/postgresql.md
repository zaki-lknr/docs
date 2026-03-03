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

## 設定

### メモリチューニング

[PostgreSQL でのメモリのチューニング—ArcMap | ドキュメント](https://desktop.arcgis.com/ja/arcmap/latest/manage-data/gdbs-in-postgresql/memory-tuning-postgresql.htm)

要約

- shared_buffers
    - 共有メモリバッファーに使用されるメモリの量を指定
    - 128KBまたは16KBにmax_connectionsの設定値をかけた最小値より大きい値を設定する必要がある
    - 大量のデータを読み込むときは、shared_buffersをデフォルト値の32MBよりも大きく設定する必要がある
- max_locks_per_transaction
    - 同時にロック可能なデータベースオブジェクトの数を指定
    - ほとんどの場合はデフォルトの64で十分だが大量データセット読み込み時はロック数が64を超えることもある
    - 同時ロックとデータセット数は1対1の関係ではなく3000のデータセット読み込み時にmax_locks_per_transactionを3000にする必要はない
- cursor_tuple_fraction
    - PostgreSQLプランナーがクエリで返される行の割合を設定
    - デフォルトは0.1で結果セット内の行(またはフィーチャ)の10%がすぐ返され、残りは返されるのにより時間がかかることを意味する
