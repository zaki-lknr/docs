# bash

## 配列

```console
$ list=("abc" "123" "xyz")
$ echo ${list[1]}
123
```

### 配列要素をループ処理

```bash
#!/bin/bash

list=("abc" "123" "xyz")
for item in ${list[@]}; do
  echo $item
done
```

### ループで参照(インデックス)

インデックスが必要であればこちら。

```bash
#!/bin/bash

list=("abc" "123" "xyz")
for i in ${!list[@]}; do
  echo ${list[$i]}
done
```

実行結果は以下の通り。

```console
abc
123
xyz
```
