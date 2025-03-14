# SQLite3

## ファイル形式

`file`で確認できる。

```console
$ file /path/to/data.db
file /path/to/data.db: SQLite 3.x database, last written using SQLite version 3043002, file counter 127, database pages 31, cookie 0x58, schema 4, UTF-8, 
```

## DBファイルを開く

```console
sqlite3 /path/to/data.db
```
