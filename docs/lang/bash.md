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

### set -* の解除

`set -u`を解除するには`set +u`

## 算術演算

```console
$ echo $((1 + 3 + 2 + 4 + 3))
13
$ echo $((13 * 2))
26
$ echo $((13 / 3))
4
$ echo $((13 % 3))
1
```

## 配列

```console
$ list=("abc" "123" "xyz")
$ echo ${list[1]}
123
```

## 制御構文

### test/評価

| オプション | 使用例               | 説明                    |
| ----- | ----------------- | --------------------- |
| `-n`  | `[ -n string ]`   | 文字列長が非ゼロであれば`true`    |
| `-z`  | `[ -z string ]`   | 文字列長がゼロでれば`true`      |
| `-e`  | `[ -e filename ]` | ファイルが存在すれば`true`      |
| `-r`  | `[ -r filename ]` | ファイルの読み取り権限があれば`true` |
| `-w`  | `[ -w filename ]` | ファイルの書き込み権限があれば`true` |
| `-x`  | `[ -x filename ]` | ファイルの実行権限があれば`true`   |

### if

```bash
if [ -z $value1 ] || [ -z $value2 ]; then
    echo '$value1 or $value2 is not defined'
elif [ -z $value3 ]; then
    echo '$value3 is not defined'
else
    echo OK
fi
```

### ループ

#### 配列要素をループ処理

```bash
#!/bin/bash

list=("abc" "123" "xyz")
for item in ${list[@]}; do
  echo $item
done
```

#### ループで参照(インデックス)

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

#### nからmまで(`seq`利用)

```bash
#!/bin/bash

for i in $(seq 1 3); do
    echo $i
done
```

実行結果は以下

```console
1
2
3
```

#### whileで無限ループ

```bash
while :
do
  # 処理
done
```
