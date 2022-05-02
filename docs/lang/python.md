# Python

## pip

### requirements.txtの利用

```txt
ansible==2.9.7
jmespath=0.10.0
```

という内容の`requirements.txt`ファイルを作成し、これで

```console
$ pip install -r requirements.txt
```

※ ファイル名は任意

### pipのアップデート

venvとか作った直後は`pip`自体のバージョンが古いので(目的にパッケージ入れるときにエラーになる前に先に)アップデートする

```console
$ pip install --upgrade pip
```

### パッケージのダウングレード

基本的には古いバージョンを指定して以下でOK

```console
$ pip install hogehoge=0.0.1
```

`requirements.txt`記載の場合も、記載した指定バージョンがインストール済みのものより古ければ自動で更新される。  
以下は`rich==11.0.0`がインストール済みの状態で`rich==9.5.1`をインストールした場合のログ。

```console
Installing collected packages: typing-extensions, rich
  Attempting uninstall: rich
    Found existing installation: rich 11.0.0
    Uninstalling rich-11.0.0:
      Successfully uninstalled rich-11.0.0
Successfully installed rich-9.5.1 typing-extensions-3.10.0.2
```

### パッケージアンインストール

```console
$ pip uninstall docker
Found existing installation: docker 5.0.3
Uninstalling docker-5.0.3:
  Would remove:
    /home/ubuntu/.local/lib/python3.8/site-packages/docker-5.0.3.dist-info/*
    /home/ubuntu/.local/lib/python3.8/site-packages/docker/*
Proceed (y/n)? y
  Successfully uninstalled docker-5.0.3
```

`-y`を付ければ強制削除になるっぽい。

```console
Uninstall Options:
  -r, --requirement <file>    Uninstall all the packages listed in the given requirements file.
                              This option can be used multiple times.
  -y, --yes                   Don't ask for confirmation of uninstall deletions.
```

## tool

### httpサーバ

```console
$ python3 -m http.server 8080
```

```console
$ python2 -m SimpleHTTPServer 8080
```

[インフラ系エンジニアが唯一覚えるべきPythonの使い方 - Qiita](https://qiita.com/zaki-lknr/items/c4b0a596890dff7ffec6)

## 実装

### 制御構文

#### for

foreach式のループ処理は以下。

```python
for item in items:
    # itemにリストの要素がセットされる
```

### リスト処理

#### リストの定義

```python
items = [
    'foo',
    'bar',
    'baz',
]
```

空リスト

```python
items = []
```

#### 要素の追加

リストオブジェクトの`append()`メソッドを使う。

```python
items.append('qux')
```

#### length

```python
array = []
array.append('2')
array.append('3')
array.append('4')

print(len(array))
```

3になる。

#### 要素のindex

```python
array = ['curry', 'beef', 'chicken', 'vegetable']

beef_index = array.index('chicken')
print(beef_index)
```

引数に指定した要素が配列中の何番目にあるかを返す。上記の出力は`2`  
存在しない場合はValueErrorになる。

```python
array = ['curry', 'beef', 'chicken', 'vegetable']

beef_index = array.index('zzz')
print(beef_index)
```

```console
$ python3 index.py
Traceback (most recent call last):
  File "index.py", line 3, in <module>
    beef_index = array.index('zzz')
ValueError: 'zzz' is not in list
```

#### 要素の有無

`in`を使う。

```python
array = ['curry', 'beef', 'chicken', 'vegetable']

print('curry' in array)
print('pork' in array)
```

出力は以下になる。

```console
True
False
```

#### スライス

```python
array = ['curry', 'beef', 'chicken', 'vegetable']

print(array[2:])
print(array[:3])
print(array[1:2])
```

実行結果は以下

```console
$ python3 slice.py
['chicken', 'vegetable']
['curry', 'beef', 'chicken']
['beef']
```

書式は`array[始点:終点]`。省略時は最初からor最後まで。  
始点はindexを「含む」が、終点はindexを「含まない」ので注意。  
(要素のindexというより、区切り位置のindexと見なすと良い)

### 文字列

#### splitlines()

テキストの末尾に改行がある場合、`split('\n')`を使うと末尾の改行もデリミタと認識し、リストの最終要素は空文字になるが、`splitlines()`であれば末尾の改行分は無視される。行ごとに処理したい場合はこちらが便利。

#### ゼロパディング

文字列のzfill()メソッドを使う。  
引数の数値の桁数長にゼロパディングされた文字列を返す。

```python
int_sample = '123'
str_sample = 'foo'
print(int_sample.zfill(8))
print(str_sample.zfill(8))
```

実行結果は以下。

```console
00000123
00000foo
```

#### 正規表現処理

##### re.sub()で置換

```python
import re

string = '/path/to/sample.txt'

# s/a/A/
string = re.sub(r'a', 'A', string)
print(string)

# 末尾の.txtを削る
string = re.sub(r'\.txt$', '', string)
print(string)

# 't'と'p'を(t)か(p)に変換
string = re.sub(r'([tp])', r'(\1)', string)
print(string)
# r'...'を使わない場合は'\\1'と書く

# aを変換。ただし大文字小文字無視
string = re.sub(r'a', r'_', string, flags=re.IGNORECASE)
print(string)
# re.sub(pattern, repl, string, count=0, flags=0) なので、countを省略するためには"flags="という名前付き引数を使う
```

上記コードの実行結果は以下の通り。

```console
$ python replace.py 
/pAth/to/sAmple.txt
/pAth/to/sAmple
/(p)A(t)h/(t)o/sAm(p)le
/(p)_(t)h/(t)o/s_m(p)le
```

### path

#### ユーザーディレクトリを展開

`os.path.expanduser`を使う。

```text
[zaki@cloud-dev2 ~]$ grep -e ^zaki -e ^root /etc/passwd
root:x:0:0:root:/root:/bin/bash
zaki:x:1000:1000:zaki:/home/zaki:/bin/bash
```

というユーザーが存在する場合、実行結果は以下の通り。

```python
[zaki@cloud-dev2 ~]$ whoami 
zaki
[zaki@cloud-dev2 ~]$ python
Python 3.10.2 (main, Jan 17 2022, 00:00:00) [GCC 11.2.1 20211203 (Red Hat 11.2.1-7)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import os.path
>>> print(os.path.expanduser('~zaki'))
/home/zaki
>>> print(os.path.expanduser('~root'))
/root
>>> print(os.path.expanduser('~'))
/home/zaki
>>> 
```

### isinstance()で型チェック

```python
bool_val = True
if (isinstance(bool_val, bool)):
    print("bool_val is bool")

int_val = 123
if (isinstance(int_val, int)):
    print("int_val is int")

list_val = [1, 2, 3]
if (isinstance(list_val, list)):
    print("list_val is list")

dict_val = {"key1": "value1", "key2": "value2"}
if (isinstance(dict_val, dict)):
    print("dict_val is dict")
```

実行結果は以下

```console
bool_val is bool
int_val is int
list_val is list
dict_val is dict
```

### 部分適用(partial application)

- `functiontools#partial()`を使う。
- 引数(の一部を)固定した関数を作る

- [Pythonのfunctools.partial(部分適用)を解説する - Qiita](https://qiita.com/tetsuya-zama/items/146d3ee9154e15158f38)
- [functools --- 高階関数と呼び出し可能オブジェクトの操作 — Python 3.9.4 ドキュメント](https://docs.python.org/ja/3/library/functools.html#functools.partial)

こんな感じ。  
<https://github.com/ansible/ansible/blob/stable-2.10/lib/ansible/playbook/base.py#L105-L114>

```python
                    method = "_get_attr_%s" % attr_name
                    if method in src_dict or method in dst_dict:
                        getter = partial(_generic_g_method, attr_name)
                    elif ('_get_parent_attribute' in dst_dict or '_get_parent_attribute' in src_dict) and value.inherit:
                        getter = partial(_generic_g_parent, attr_name)
                    else:
                        getter = partial(_generic_g, attr_name)

                    setter = partial(_generic_s, attr_name)
                    deleter = partial(_generic_d, attr_name)
```

なるほどわからん

[[Ansible] そのtag設定、想定通りに動いてますか？ (継承機能とその実装を確認する) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/03/24/100317)

### メタクラス

- [pythonで考えるメタクラスとは？ - Qiita](https://qiita.com/slope-book/items/3319c357c1a092d9d841)
- [__new__と__init__とメタクラスと - Qiita](https://qiita.com/FGtatsuro/items/49f907a809e53b874b18)

クラスの定義をコードで動的に実装する感じ。

<https://github.com/ansible/ansible/blob/stable-2.10/lib/ansible/playbook/base.py#L105-L114>  

```python
                    method = "_get_attr_%s" % attr_name
                    if method in src_dict or method in dst_dict:
                        getter = partial(_generic_g_method, attr_name)
                    elif ('_get_parent_attribute' in dst_dict or '_get_parent_attribute' in src_dict) and value.inherit:
                        getter = partial(_generic_g_parent, attr_name)
                    else:
                        getter = partial(_generic_g, attr_name)

                    setter = partial(_generic_s, attr_name)
                    deleter = partial(_generic_d, attr_name)
```

- [[Ansible] そのtag設定、想定通りに動いてますか？ (継承機能とその実装を確認する) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/03/24/100317)

## 仮想環境

### venv

#### 作成

```console
$ python3 -m venv venv/ansible
```

#### systemのパッケージパスも参照

```console
$ python3 -m venv venv-sys --system-site-packages
```

### pyvenv

RHEL8に入ってるやつ。

```console
[zaki@rhel8-node ~]$ pyvenv-3 venv3
WARNING: the pyenv script is deprecated in favour of `platform-python -m venv`
[zaki@rhel8-node ~]$ . venv3/bin/activate
(venv3) [zaki@rhel8-node ~]$ 
(venv3) [zaki@rhel8-node ~]$ pip install --upgrade pip
Cache entry deserialization failed, entry ignored
Collecting pip
  Downloading https://files.pythonhosted.org/packages/47/ca/f0d790b6e18b3a6f3bd5e80c2ee4edbb5807286c21cdd0862ca933f751dd/pip-21.1.3-py3-none-any.whl (1.5MB)
    100% |████████████████████████████████| 1.6MB 641kB/s 
Installing collected packages: pip
  Found existing installation: pip 9.0.3
    Uninstalling pip-9.0.3:
      Successfully uninstalled pip-9.0.3
Successfully installed pip-21.1.3
(venv3) [zaki@rhel8-node ~]$ pip list
Package    Version
---------- -------
pip        21.1.3
setuptools 39.2.0
```

`--system-site-packages`も併用可

```console
[zaki@rhel8-node ~]$ pyvenv-3.6 venv --system-site-packages
WARNING: the pyenv script is deprecated in favour of `platform-python -m venv`
[zaki@rhel8-ansible-node ~]$ . venv/bin/activate
(venv) [zaki@rhel8-node ~]$ pip list
DEPRECATION: The default format will switch to columns in the future. You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
cffi (1.11.5)
chardet (3.0.4)
configobj (5.0.6)
configshell-fb (1.1.28)
cryptography (3.2.1)
dbus-python (1.2.4)
decorator (4.2.1)
ethtool (0.14)
:
:
```

## RHEL / Ansible

[Ansible が利用する Python 実行環境 - 赤帽エンジニアブログ](https://rheb.hatenablog.com/entry/ansible_python_environment)
