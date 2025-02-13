# jq

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
