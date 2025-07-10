# Python

## tool

### JSON整形

```console
$ ... | python -m json.tool
```

### httpサーバ

```console
$ python3 -m http.server 8080
```

```console
$ python2 -m SimpleHTTPServer 8080
```

[インフラ系エンジニアが唯一覚えるべきPythonの使い方 - Qiita](https://qiita.com/zaki-lknr/items/c4b0a596890dff7ffec6)

## 実装

### 演算子

#### セイウチ演算子(:=)

代入と評価を同時に行う。

```python
while (items := get_items()):
    :
    :
```

`items`がtrueである間(リストを返す関数であれば空でない限り)はループする。

#### 比較(==)

Pythonは文字列も`==`を使う。  
以下の比較処理は真。

```python
string = "foobar"

if string == "foobar":
    print("string is 'foobar'")
```

配列の比較も`==`で行う。以下はtrue

```python
obj_list = [1, 2, 3, "abc"]

if obj_list == [1, 2, 3, "abc"]:
    print("true")
```

というか`==`はオブジェクトの比較に使えるってことかな。

### 制御構文

#### for

foreach式のループ処理は以下。

```python
for item in items:
    # itemにリストの要素がセットされる
```

ワンライナーでも書ける。  
ループ処理した結果をリストにセットしたい場合など。  
その際は`[...]`で囲む。

```python
result = [ ..itemに対する処理.. for item in items]
```

BeautifulSoupを使ったスクレイピングの記述例

```python
links = [li.find('a').get('href') for li in s.find_all('li', class_='toctree-l2')]
print(links)
```

#### break

ループを途中で抜けるには`break`

#### continue

次のループへ飛ぶには`continue`

#### exit

Pythonのexitは組み込みでなく`sys`をimportする必要がある。

```python
import sys

sys.exit(1)
```

### 変数

#### 環境変数の参照

```python
import os

aws_access_key = os.environ.get("AWS_ACCESS_KEY_ID")
if aws_access_key is None:
    print("AWS_ACCESS_KEY_ID not defined")
```

#### 型の確認

```python
type(value)
```

#### isinstance()で型判定

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

`list`型でもスカラー値でも引数に取れる関数の実装などで実行時に判定、などで使える。

#### None判定

```python
if data is not None:
    # dataがNoneでない場合
```

### 引数

#### コマンドライン引数

`sys.argv`を使う

```python
import sys

args = sys.argv
print(args)
print(args[0])
print(args[1])
```

```console
$ ./argv.py hello
['./argv.py', 'hello']
./argv.py
hello
```

### 関数

#### main

```python
#!/usr/bin/python3

def func():
    print("begin func()")

if __name__ == "__main__":
    print("begin main()")
    func()
```

### assert

特に`import`は不要で使用可能

```python
# aの値が10であること
assert(a == 10)
```

### 配列

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

末尾への追加はリストオブジェクトの`append()`メソッドを使う。

```python
items.append('qux')
```

末尾以外の任意の位置への追加は`insert()`で位置指定する。  
`0`は先頭。

```python
items.insert(0, item)
```

#### リストの結合

複数のリストを結合(1つのリストに全要素をまとめる)するには`+`を使う

```python
list1 = ['foo', 'bar', 'baz']
list2 = [1, 2, 3]

print(list1 + list2)
```

実行結果は`['foo', 'bar', 'baz', 1, 2, 3]`になる。  
`+=` で代入も可能。

#### length

```python
array = []
array.append('2')
array.append('3')
array.append('4')

print(len(array))
```

3になる。

#### 空判定

空の配列変数はそれ自体がFalseになる。

```python
empty_array = []

if not empty_array:
    print("blank")
```

あるいは`[]`との一致でも判定できる。

```python
if empty_array == []:
    print("blank")
```

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

#### 連番の定義

`range(開始, 終了)`を使う

```python
r = range(0, 9)
```

これで`r`は`[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]`となる。  
正確にはrange型のためリストではないが、`list(r)`を使うとリストになる。

1引数の場合は開始`0`とみなされる。(0開始の場合は終了の値のみでOK。↑は`range(9)`と同値)

### 集合

集合(set)は重複不可で順序を保持しない。

#### 集合の定義

```python
data_set = {200, 201, 202, 203, 204, 301, 401, 403, 404}
print(type(data_set))
# 出力は <class 'set'>
```

#### 空集合の定義

`{}`は不可(辞書と同じコードになるため)  
`set()`を使う。

```py
data = set()
```

### 辞書

#### 辞書の定義

```python
dict1 = {"key1": "value1", "key2": "value2"}
dict2 = dict(key1="value1", key2="value2")
```

この場合、`dict1`と`dict2`の中身は同じ。  
`dict1 == dict2`の結果もtrueになる。

#### キーを使った値へのアクセス

```py
dict1['key1']
```

ただしキーが無い場合は`KeyError`例外になる。  
`get()`を使えばキーが存在しない場合は`None`が返る。

```py
dict1.get('key1')
```

#### キーの追加

`dict[key] = value`で足してやればOK

```py
>>> dict1 = {"key1": "value1", "key2": "value2"}
>>> print(dict1)
{'key1': 'value1', 'key2': 'value2'}
>>> dict1['key3'] = "value3"
>>> print(dict1)
{'key1': 'value1', 'key2': 'value2', 'key3': 'value3'}
>>> 
```

新規キーの追加をサブキーまで左辺で指定すると KeyErrorになる。その場合は辞書型データを代入すればOK

```py
>>> dict1['key4']['foo'] = "bar"
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'key4'
```

```py
>>> dict1['key4'] = {'foo': "bar" }
>>> print(dict1)
{'key1': 'value1', 'key2': 'value2', 'key3': 'value3', 'key4': {'foo': 'bar'}}
>>> 
```

#### 辞書要素のループ

##### キーと値の組み合わせを取得

```python
sample_dict = {
    "key1": "value1",
    "key2": "value2",
    "key3": "value3",
}

for k, v in sample_dict.items():
    print("key: " + k + ", value: " + v)
```

`辞書.items()`で、キーと値のペアのリストを得られるので、これでループできる。  
上のコードの実行結果は以下の通り。

```console
key: key1, value: value1
key: key2, value: value2
key: key3, value: value3
```

##### 辞書の値のみをループ

`values()`を使う

```python
for v in sample_dict.items():
    print(v)
```

実行結果は以下の通り。

```console
value1
value2
value3
```

#### 辞書のリストから指定キーの値の一致判定

リスト内包表記を使って辞書から指定キーのみのリストを作り、それに対して`in`で判定する。

```python
if data in (i.get('name') for i in items):
    print(data + ' is exists')
```

#### 辞書->YAML化

```python
import yaml

print(yaml.dump(dict_data, default_flow_style=False))
```

子要素がフロースタイルになってブロックスタイルにならない場合は`default_flow_style`のオプションを`False`指定する。

#### YAMLファイル->辞書化

```python
import yaml

with open('config.yaml', 'r') as yml:
    config = yaml.safe_load(yml)
```

これで`config`変数にYAMLファイルに定義した情報が辞書型オブジェクトとしてセットされる。

#### 辞書->JSON化

```python
import json

print(json.dumps(dict_data))
```

日本語が`\u****`になるのを抑止するには非ASCII文字のエスケープ処理を無効化するオプションを追加。(デフォルトはon)

```python
json.dump(obj, ensure_ascii=False)
```

#### JSON文字列->辞書(オブジェクト)

```python
import json

obj = json.loads(json_strings)
```

YAMLと違ってメソッド名は`dumps()`で`s`が付与される。  
`dump()`は、ファイル出力を行うメソッドとして別に存在する。

#### JSONファイル->オブジェクト

上記の`json.loads()`と異なり、`json.load()`を使う。

```python
import json

with open('./sample.json') as f:
    obj = json.load(f)
```

STDINの場合は

```python
import json

with sys.stdin as f:
    obj = json.load(f)
```

### 数値

#### 乱数

> ランダムな浮動小数点数（範囲は 0.0 <= X < 1.0）を返します
>
> <https://docs.python.org/ja/3/library/random.html#random.random>

```python
import random

r = random.random()
print(r)
```

出力は`0.5230624471209939`など

### 文字列

#### 指定文字で始まるか

```python
if string_value.startswith("kube"):
    # 文字列が"kube"で始まる場合
```

#### 末尾1文字

```python
sample_str[-1]
```

#### 末尾1文字を削った文字列

```python
sample_str[:-1]
```

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

#### 文字列置換

正規表現を使わないなら`replace()`で置換する。

```py
string = '2025/06/22'
d = string.replace('/', '-')

print(string)
print(d)
```

出力は以下の通り。

```console
2025/06/22
2025-06-22
```

#### 正規表現処理

##### re.search()で文字列判定

```python
import re

if re.search(regexp, string):
    # stringがregexpにマッチ
```

`re.match()`は先頭からマッチを行うため、文字列の途中にはマッチしない。

##### 正規表現検索とキャプチャ

```python
r = re.match(r'aaa(\d{5})bbb', string)
```

`r`には`re.Match`オブジェクトが返る。マッチしなかったら`None`  
マッチした場合、`\d{5}`は`r.group(1)`で参照できる。

##### 名前付きキャプチャ

括弧のみだとインデックス番号でのアクセスになるが、パターン側に名前を付与しておけば名前でアクセスできる。  
書式は `(?P<name>regexp)`

```python
import re

string = '/path/to/sample.txt'

result = re.match(r'(?P<path>.*)/(?P<file>[^\.]*)\.(?P<ext>.*)', string)
if result:
    print("path: " + result.group(1))
    print("file: " + result.group(2))
    print("ext: " + result.group(3))
    print("---")
    print("path: " + result.group('path'))
    print("file: " + result.group('file'))
    print("ext: " + result.group('ext'))
```

出力はグループ番号・グループ名どちらもpathは`/path/to`、fileは`sample`、extは`txt`になる。

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

#### バイト列との変換

バイト列 -> 文字列

```py
byte = b'nijigasaki'
str = byte.decode()

print(byte)
print(str)
```

出力は

```console
b'nijigasaki'
nijigasaki
```

### 日付型(datetime)

#### 現在時刻

```py
import datetime

date_now = datetime.datetime.now()
print(date_now)
```

出力は `2025-06-23 23:05:55.796387` など。

#### 時刻加減算

datetime型の変数に任意の加減算を行うには`datetime.timedelta`を使う。

```py
import datetime

date_now = datetime.datetime.now()
print(date_now)

custom = date_now + datetime.timedelta(days=1, hours=-1, minutes=5)
print(custom)
```

出力は以下の通り、「+1日、-1時間、+5分」となる。

```console
2025-06-24 21:51:20.140227
2025-06-25 20:56:20.140227
```

#### 時刻差分

datetime型変数同士の差分は`datetime.timedelta`となる。

```py
import datetime

date_now = datetime.datetime.now()
custom = date_now + datetime.timedelta(days=2, hours=-1, minutes=5)

diff = custom - date_now
print(type(diff))
print(diff)

print(str(diff.days) + " days")
print(str(diff.seconds) + " seconds")
```

出力は以下の通り。

```console
<class 'datetime.timedelta'>
1 day, 23:05:00
1 days
83100 seconds
```

<https://docs.python.org/ja/3/library/datetime.html#datetime.timedelta>

`seconds`は`days`の余りのような位置づけの値のため、全秒数を得たい場合は`total_seconds()`を使う。これはメソッド。

#### UNIX Epoch -> datetime

`fromtimestamp()`を使えばOK

```py
import datetime

dt = datetime.datetime.fromtimestamp(0)
print(dt)

dt = datetime.datetime.fromtimestamp(1750763540)
print(dt)
```

出力は以下

```console
1970-01-01 09:00:00
2025-06-24 20:12:20
```

#### datetime -> UNIX Epoch

`timestamp()`を使う。  
float型なので必要に応じて`int()`を併用する。

```py
# 30日前のUNIX時刻
sec = int((datetime.datetime.now() + datetime.timedelta(days=-30)).timestamp())
```

### byte型

変数出力すると`b'....'`と表記され出力される。

```python
byte_data = b'zzzzzzzz'

print(byte_data)
print(len(byte_data))
print(type(byte_data))
```

出力は以下。

```console
b'zzzzzzzz'
8
<class 'bytes'>
```

#### byte -> string変換

```python
string_data = byte_data.decode()

print(string_data)
print(type(string_data))
```

出力は`<class 'str'>`

#### string -> byte変換

```python
byte_data = string_data.encode()
print(byte_data)
print(type(byte_data))
```

これで出力は`<class 'bytes'>`

### ハッシュ関数

#### MD5

```python
import hashlib

m = hashlib.md5()
m.update(b'aaa')
d = m.hexdigest()
print(d)
```

出力は`47bce5c74f589f4867dbd57e9ca9f808`になる。  
以下と同値。

```console
$ printf aaa | md5sum 
47bce5c74f589f4867dbd57e9ca9f808  -
```

```python
d = m.digest()
```

この場合の値はバイナリとなり、出力は以下の通り。

```console
b'G\xbc\xe5\xc7OX\x9fHg\xdb\xd5~\x9c\xa9\xf8\x08'
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

### 外部コマンド実行

`subprocess.run()`を使う。(`os.system`はもう使わない)

```python
import subprocess

ret = subprocess.run(["ls", "-l"], capture_output=True, text=True)
print(ret.stdout)
```

リストでなく文字列を指定したい場合は`shell=True`を付与

```python
subprocess.run("ls -l", shell=True)
```

### 出力

#### 標準エラー出力

```python
import sys

print("error", file=sys.stderr)
```

#### printで改行無し

```python
print("sample", end="")
```

ただし行ごとにフラッシュされるため、プログレス表示的に使う場合は追加オプションが必要

```python
print(".", end="", flush=True)
```

#### ヒアドキュメント

```python
print("""
東京・お台場にある、自由な校風と専攻の多様さで人気の高校「虹ヶ咲学園」。
スクールアイドルの魅力にときめいた普通科2年の高咲侑は、
幼馴染の上原歩夢とともに「スクールアイドル同好会」の門を叩く。
""")
```

この場合、「開始の`"""`」のあとの改行と、「終了の`"""`」の手前の改行も含まれる。

### ファイルI/O

`with`ブロックを抜けると自動的にcloseされる。  
また、`with`の内側の変数は外側でも使える。

#### 文字列としてread

```python
with open(file_path) as f:
    data = f.read()
```

#### 文字列としてwrite

```python
with open(file_path, mode='w') as f:
    f.write(data)
```

### 通信

#### HTTP(requests)

`requests`を使うと楽。

```python
import requests
r = requests.get("http://www.example.org")
```

##### POST

リクエストボディは`data`で指定する。  
辞書型データをJSON形式のリクエストボディにするなら`json.dumps`を使うと楽。

```python
payload = json.dumps(dict_params)
headers = {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
    'Authorization': 'Bearer ' + token
}
requests.post(url, data=payload, headers=headers)
```

##### DELETE

POSTと同じ要領

```python
requests.delete(url)
```

##### リクエストヘッダ

```python
headers = {
    'Accept': 'application/json',
    'Authorization': 'Bearer ********'
}

requests.get(url, headers=headers)
```

##### レスポンス

```python
print(r.status_code)  # ステータスコード(200など)

print(r.request)      # Requestオブジェクト
print(r.request.url)  # アクセスしたURL

print(r.text)   # レスポンスbody
```

REST APIのようにレスポンスがJSON形式のテキストの場合は、`r.json()`を使うとオブジェクトとして値をとれる。

```python
for item in r.json()['items']:
    print(item['metadata']['name'])
```

##### SSL検証無視

```python
r = requests.get("https://www.example.org", verify=False)
```

##### Basic認証

```python
requests.get(url, auth=HTTPBasicAuth(username, password))
```

##### 接続タイムアウト設定

```python
timeout_sec = 3
requests.get(url, timeout=timeout_sec)
```

##### 接続エラーのハンドリング

例外が発生するのでそれをcatchする。

```python
try:
    requests.post(url)
except (requests.ConnectionError, requests.ConnectTimeout) as e:
    print(e)
```

#### HTTP (urllib.request)

requestsを追加できない場合など。

##### GET

```python
import urllib.request

url = 'https://example.org'
header = {
    'Authorization': 'Bearer ' + token
}

req = urllib.request.Request(url, headers=headers)
with urllib.request.urlopen(req) as res:
    j = json.load(res)
```

##### POST

Requestオブジェクト作成時に送信するデータを指定すればPOSTメソッドになる。

```python
auth_data = {
    'Username': username,
    'Password': password,
}
auth_req = urllib.request.Request(auth_page, urllib.parse.urlencode(auth_data).encode('ascii'))
with urllib.request.urlopen(auth_req) as auth_resp:
    print(auth_resp.read())
```

##### レスポンスヘッダ

```python
req = urllib.request.Request(url, headers=headers)
with urllib.request.urlopen(req) as res:
    body = res.read()
    headers = res.getheaders()    # dict形式で取れる
```

##### SSL検証無視

```python
import ssl
context = ssl._create_unverified_context()

r = urllib.request.Request(url, headers=headers)
while urllib.request.urlopen(req, context=context) as res:
    ...
```

### 例外

#### 例外の捕捉

`try`,`except`を使う

```python
try:
    # 例外が発生するコード
except RuntimeError as e:
    # エラーハンドリング処理
    # エラー情報は"e"に入っている
    print(e)
```

捕捉する例外が複数の場合は、`except`を複数並べるか、カッコでまとめる。

```python
try:
    # 例外が発生するコード
except (RuntimeError, TypeError) as e:
    print(e)
```

#### 例外を投げる(raise)

```python
raise RuntimeError('application error: ' + error_value)
```

標準で使用できる組み込み例外は以下。  
[組み込み例外](https://docs.python.org/ja/3/library/exceptions.html)

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

### クラス

#### コンストラクタ(__init__)

```py
class Animal:
    def __init__(self, a):
        self.__nakigoe = a
```

#### インスタンス作成(呼び出し側)

```py
import animal

a = animal.Animal('piyo')
```

## 環境

### pip

#### バージョン指定

```console
pip install ansible==9
```

「以上」の場合は以下

```console
pip install ansible>=8
```

#### requirements.txtの利用

```txt
ansible==2.9.7
jmespath==0.10.0
```

という内容の`requirements.txt`ファイルを作成し、これで

```console
$ pip install -r requirements.txt
```

※ ファイル名は任意

#### pipのアップデート

venvとか作った直後は`pip`自体のバージョンが古いので(目的にパッケージ入れるときにエラーになる前に先に)アップデートする  
※ そもそもvenv作成時に `--upgrade-deps` をつけておけば自動更新されて環境が作成される。

```console
$ pip install --upgrade pip
```

#### パッケージのダウングレード

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

#### パッケージアンインストール

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

#### 全てアンインストール

```console
$ pip uninstall -r <(pip freeze)
```

#### 依存関係の確認

```console
$ pip show ansible-core
:
:
Requires: cryptography, jinja2, packaging, PyYAML, resolvelib
Required-by: ansible
```

Required-byの`ansible`によってインストールされ、このパッケージによって`cryptography`, `jinja2`, `packaging`, `PyYAML`, `resolvelib`がインストールされる。

### pyenv

[pyenv/pyenv: Simple Python version management](https://github.com/pyenv/pyenv)

#### インストール

```console
curl https://pyenv.run | bash
```

#### 設定追加

```console
cat <<'__EOL__' >> $HOME/.bashrc
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
__EOL__
. $HOME/.bashrc
```

#### 依存パッケージのインストール

以下はUbuntuの場合。

```console
sudo apt update; sudo apt install build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev curl \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

その他の環境はwiki参照: <https://github.com/pyenv/pyenv/wiki#suggested-build-environment>

#### Pythonインストール

```console
pyenv install 3.11.3
```

インストール可能なPythonは`pyenv install -l`で確認

#### `python`の切替

```console
pyenv global 3.11.3
```

切替可能なPythonは`pyenv versions`で確認

### venv

#### 作成

```console
$ python3 -m venv venv/ansible
```

#### 作成時にpipとsetuptoolsを更新する

```console
$ python3 -m venv ~/venv/dev --upgrade-deps
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
