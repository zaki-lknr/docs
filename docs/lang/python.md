# Python

## pip

### pipのアップデート

venvとか作った直後は`pip`自体のバージョンが古いので(目的にパッケージ入れるときにエラーになる前に先に)アップデートする

```console
$ pip install --upgrade pip
```

## 実装

### リスト処理

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
