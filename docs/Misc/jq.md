# jq

## 全てのキーの取得

```console
command | jq keys
```

ただし元の順序は保持されずにソートされる。  
順序を保持するには以下

```console
command | jq keys_unsorted
```

## 全ての値の取得

```console
command | jq '.[]'
```

## 特定の値を取得

```console
command | jq '.list[0].key'
```

## 配列要素を全て出力

```console
command | jq '.list[].key'
```

## 出力からクォーテーションを削除

`-r`を付与

```console
command | jq -r '.list[0].key'
```
