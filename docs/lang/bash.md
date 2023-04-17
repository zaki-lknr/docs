# bash

## 配列

```console
$ list=("abc" "123" "xyz")
$ echo ${list[1]}
123
```

### ループで参照(インデックス)

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
