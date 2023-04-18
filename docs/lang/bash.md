# bash

## シェルオプション

[【 set 】コマンド――シェルの設定を確認、変更する：Linux基本コマンドTips（205） - ＠IT](https://atmarkit.itmedia.co.jp/ait/articles/1805/10/news023.html)

`set`や、shebangで`#!/bin/bash -u`などでセットする

### 未定義変数使用時にエラー

`-u` (nounset)

```console
[zaki@cloud-dev2 sh]$ cat set.sh 
#!/bin/bash -u

echo $hoge
[zaki@cloud-dev2 sh]$ ./set.sh 
./set.sh: 行 3: hoge: 未割り当ての変数です
[zaki@cloud-dev2 sh]$ export hoge=123
[zaki@cloud-dev2 sh]$ ./set.sh 
123
```

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
