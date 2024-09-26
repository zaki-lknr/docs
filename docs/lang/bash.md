# bash

## シェルオプション

[【 set 】コマンド――シェルの設定を確認、変更する：Linux基本コマンドTips（205） - ＠IT](https://atmarkit.itmedia.co.jp/ait/articles/1805/10/news023.html)

`set`や、shebangで`#!/bin/bash -u`などでセットする

### 未定義変数使用時にエラー (-u)

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

### コマンドと引数を表示 (-x)

```console
$ cat option.sh 
#!/bin/bash -x

ls /
$ ./option.sh 
+ ls /
afs  boot  etc   lib    media  opt   root  sbin  sys  usr
bin  dev   home  lib64  mnt    proc  run   srv   tmp  var
```

出力の先頭文字は変数`PS4`で指定。

```console
$ PS4="---" ./option.sh 
---ls /
afs  boot  etc   lib    media  opt   root  sbin  sys  usr
bin  dev   home  lib64  mnt    proc  run   srv   tmp  var
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

インクリメント

```console
    count=$((++count))
```

## 配列

```console
$ list=("abc" "123" "xyz")
$ echo ${list[1]}
123
```

## 関数

### 引数

```bash
#!/bin/bash

function myfunc() {
    echo "function arg1($1) arg2($2)"
}

myfunc a b
```

### 戻り値

```bash
function myfunc() {
    echo "function arg1($1) arg2($2)"
    return 1
}

myfunc a b
echo $?
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
| `-eq` | `[ $n -eq 123 ]`  | 数値比較(一致)              |
| `-ne` | `[ $n -ne 200 ]`  | 数値比較(不一致)             |
| `==`  | `[ $s == "abc" ]` | 文字列比較(一致)             |
| `!=`  | `[ $s != "xyz" ]` | 文字列比較(不一致)            |

- 文字列一致は `=` も有効。
- 文字列の空文字判定は `[ $s == "" ]` は不可(`$s`が空の場合 `[ == "" ]`と解釈される)
    - `-z`を使うか`[ "$s" == "" ]`と書く

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

#### nからmまで(ブレース)

```bash
for i in {1..10}; do
    echo $i
done
```

#### whileで無限ループ

```bash
while :
do
  # 処理
done
```

#### ファイルを1行ずつ

```bash
while read line
do
    # ${line}に行が入っている
done < "/path/to/file.txt"
```

#### コマンドの結果を1行ずつ

```bash
command | filter | while read line
do
    # ${line}の処理
done
```

#### 途中で抜ける

`break`

#### 次のループ

`continue`
