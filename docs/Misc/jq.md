# jq

## 特定の値を取得

```console
command | jq '.list[0].key'
```

## 出力からクォーテーションを削除

`-r`を付与

```console
command | jq -r '.list[0].key'
```
