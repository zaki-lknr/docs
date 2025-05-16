# フィルタ

ドキュメントはAnsibleとJinja2両方チェックする。

- [Using filters to manipulate data — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)
- [Template Designer Documentation — Jinja Documentation (3.1.x)](https://jinja.palletsprojects.com/en/latest/templates/#list-of-builtin-filters)

項目やコレクションによっては個別のドキュメントもある。

- [ipaddr filter — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters_ipaddr.html)
- [community.general Filter Guide — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/community/general/docsite/filter_guide.html)

実装はこのあたり

- ansible.builtin
    - [ansible/core.py at devel · ansible/ansible](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/filter/core.py)
    - core以外のものは同じディレクトリの別ソースにあるかも
- jinja2
    - [jinja/filters.py at main · pallets/jinja](https://github.com/pallets/jinja/blob/main/src/jinja2/filters.py)

## デフォルト値 (default)

未定義だったらこの値に置き換える、というもの。  
以下は`foobar_values`が未定義の場合は`value`は`zzz`となる。

```yaml
vars:
  value: "{{ foobar_values | default('zzz') }}"
```

よく使うのは初期値が無い場合にループでリストを作るとき。

```yaml
    - name: curry
      debug:
        msg: "{{ item }}"
      loop: "{{ yasai + (niku | default([])) }}"
```

とか

```yaml
    - name: set_fact list value
      vars:
        list_values:
        - item1
        - item2
        - item3
      set_fact:
        list_items: "{{ list_items|default([]) + [item] }}"
      loop: "{{ list_values }}"
```

未定義でなくfalseだったら置き換えるのであれば、第2引数に`true`をセットする。

```yaml
{{ ''|default('the string was empty', true) }}
```

- [jinja-filters.default](https://jinja.palletsprojects.com/en/3.0.x/templates/#jinja-filters.default)
- [[Ansible] 省略したいパラメタに変数指定せざるを得ない場合に使う変数omitとdefaultフィルタ - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/06/02/090251)
- [[Ansible] リスト連結時にdefaultを使って未定義の場合は空リストとして処理する - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/05/09/224713)

## 三項演算子

`ternary`を使う。

[Defining different values for true/false/null (ternary)](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#defining-different-values-for-true-false-null-ternary)

```yaml
    - name: ternary sample
      debug:
        msg: "{{ (sample_value == 1) | ternary('sample_valueは1だよ', 'sample_valueは1じゃないよ') }}"
      # やってることは (sample_value == 1) ? 'sample_valueは1だよ': 'sample_valueは1じゃないよ' と同じ

    - name: ternary nest sample
      debug:
        msg: "{{ (sample_value == 1) |
                    ternary('sample_valueは1だよ',
                              (sample_value == 2) | ternary('sample_valueは2だよ', 'sample_valueは1でも2でもないよ')
                            )
              }}"
        # 入れ子にしたternary()

    - name: ternary nest sample
      debug:
        msg: "{{ (sample_value == 1) |
                    ternary('sample_valueは1だよ', undefined) |
                  default((sample_value == 2) |
                           ternary('sample_valueは2だよ', 'sample_valueは1でも2でもないよ')
                         )
              }}"
      # 入れ子にせずに、defaultフィルタを併用。ピタゴラスイッチ感。
      # if-elsif-else っぽく見せるインデント難しい
```

## 辞書を`{ key=辞書のキー名, value=辞書のvalueデータ }`のリストに変換

`dict2items`を使う。

[Transforming dictionaries into lists](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#transforming-dictionaries-into-lists)

```yaml
---
- hosts: localhost
  gather_facts: false
  
  tasks:
  - name: dict2items sample
    vars:
      menu:
        curry:
          vegetable:
            - carrot
            - potato
            - onion
          meat:
            - cow
          chicken:
        pasta:
          vegetable:
            - spinach
            - garlic
    debug:
      msg: "{{ menu | dict2items }}"
```

実行結果は以下の通り。

```json
"msg": [
    {
        "key": "curry",
        "value": {
            "chicken": null,
            "meat": [
                "cow"
            ],
            "vegetable": [
                "carrot",
                "potato",
                "onion"
            ]
        }
    },
    {
        "key": "pasta",
        "value": {
            "vegetable": [
                "spinach",
                "garlic"
            ]
        }
    }
]
```

[ansible-sample/dict2items.yml at master · zaki-lknr/ansible-sample](https://github.com/zaki-lknr/ansible-sample/blob/master/filter/dict2items.yml)

## 検索 (select / selectattr)

`grep`的な動作。

[[Ansible / Jinja2] select / selectattr を使った配列と辞書のフィルタリング - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/02/18/000228)

入れ子の場合もそのまま書けばOK

```yaml
    summary_fields:
      inventory:
        name: netbox-demo-inventory
```

という構造に対しては、以下のように書く。

```jinja2
selectattr('summary_fields.inventory.name', 'eq', 'netbox-demo-inventory')
```

## リスト要素への一律処理 (map)

### 別フィルタの適用

```yaml
"{{ items | map('length') }}"
```

リスト`items`の各要素に対して`length`フィルタをかます

### 要素抜き出し

辞書のキーを`attribute`で指定することで、各要素(辞書型からなる)の指定キーの値のみ抜き出す。

```yaml
"{{ items | map(attribute='hostname') }}"
```

キーが無い場合は`AnsibleUndefined`になるが、オプションか更にフィルタすることで対処できる。  
`default`を指定すればキーが無い場合はその値に置き換える。

```yaml
"{{ items | map(attribute='hostname', default='no data') }}"
```

`select`を使ったフィルタに繋げれば、undefの項目を除外もできる。

```yaml
"{{ items | map(attribute='hostname') | select('none') }}"
```

`none`は[Jinja2のTest](https://jinja.palletsprojects.com/en/latest/templates/#jinja-tests.none)の一つ。

## リストの先頭・末尾要素

`array[0]`, `array[-1]`でも一緒といえば一緒だけど。  
出展はJinja2のフィルタ。

- [first](https://jinja.palletsprojects.com/en/latest/templates/#jinja-filters.first)
- [last](https://jinja.palletsprojects.com/en/latest/templates/#jinja-filters.last)

```yaml
    - name: first
      vars:
        list:
          - item1
          - item2
          - item3
        foobar: hogehoge
      debug:
        msg:
          - "{{ list | first }}"
          - "{{ list[0] }}"
          - "{{ list | last }}"
          - "{{ list[-1] }}"
```

結果

```
TASK [first] *******************************************
ok: [localhost] => 
  msg:
  - item1
  - item1
  - item3
  - item3
```

ちなみに、文字列型の場合でもiterableなので、一文字目・最後の文字が取れる。

## リストから要素の除外(difference)

リストから特定要素を除外するには`difference`フィルタを使う。  
入力のリストの要素から、`difference`フィルタの引数に指定したリストの要素を除外したリストを返す。  
`difference`にもリストを指定する必要がある(iterableな変数でなければエラーとなる)

```yaml
---
- hosts: localhost
  vars:
    int_list1: [1, 2, 3]
    int_list2: [3, 4]

  tasks:
  - name: defference filter sample
    debug:
      msg:
      - "{{ int_list1 | difference(int_list2) }}"
```

出力は`[1, 2]`になる。

なお、文字列の場合は元々iterableのため、`difference`の引数はリストでなくても一応動作する。

```yaml
---
- hosts: localhost
  vars:
    list1: ["foo", "bar", "baz"]
    list2: ["baz", "qux"]
    str_item: "baz"

  tasks:
  - name: defference filter sample
    debug:
      msg:
      - "{{ list1 | difference(list2) }}"
      - "{{ list1 | difference(str_item) }}"
```

どちらの結果も`["foo", "bar"]`になる。

## 辞書のマージ(combine)

```yaml
- name: merge 2 dicts
  vars:
    var1:
      list1:
        - item1
        - item2
    var2:
      list2:
        - itemA
        - itemB
  debug:
    msg: "{{ var1 | combine(var2) }}"
```

2つの辞書をマージする。  
重複キーの無い辞書同士は単純合成になり、上のタスクの実行結果は以下の通り。

```console
TASK [merge 2 dicts] *********************************************
ok: [localhost] => {
    "msg": {
        "list1": [
            "item1",
            "item2"
        ],
        "list2": [
            "itemA",
            "itemB"
        ]
    }
}
```

同じキーを含む場合は上書きする。

```yaml
- name: merge 2 dicts
  vars:
    var1:
      list1:
        - item1
        - item2
    var2:
      list1:
        - item3
      list2:
        - itemA
        - itemB
  debug:
    msg: "{{ var1 | combine(var2) }}"
```

`combine`する辞書に同じ`lsit1`キーを含む場合、このタスクの実行結果は以下の通りで、`combine`に指定した辞書側の`list1`キーの値で上書きされる。

```console
TASK [merge 2 dicts] ******************************************************************
ok: [localhost] => {
    "msg": {
        "list1": [
            "item3"
        ],
        "list2": [
            "itemA",
            "itemB"
        ]
    }
}
```

### list_merge

辞書の要素がリストの場合、キーが同じ場合の上書き動作のデフォルトは、`list_merge`パラメタで動作を変更できる。

`append`を指定すると、要素の末尾に追加。

```yaml
- name: merge 2 dicts
  vars:
    var1:
      list1:
        - item1
        - item2
    var2:
      list1:
        - item3
      list2:
        - itemA
        - itemB
  debug:
    msg: "{{ var1 | combine(var2, list_merge='append') }}"
```

`list1`キーが重複した入力で、`list_merge=append`を指定した場合の実行結果は以下の通り。

```console
TASK [merge 2 dicts] *********************************************
ok: [localhost] => {
    "msg": {
        "list1": [
            "item1",
            "item2",
            "item3"
        ],
        "list2": [
            "itemA",
            "itemB"
        ]
    }
}
```

`list1`キーの値のリストもマージ(`combime`の引数に指定する方が末尾に追加)されている。  
`list_merge`に指定できるパラメタは以下の通り。

| パラメタ         | 動作                 |
| ------------ | ------------------ |
| `append`     | 末尾に追加              |
| `prepend`    | 先頭に追加              |
| `append_rp`  | 末尾に追加 (重複時に既存要素は削除) |
| `prepend_rp` | 先頭に追加 (重複時に既存要素は削除) |

`append_rp`の動作は以下の通り。

```yaml
- name: merge 2 dicts
  vars:
    var1:
      list1:
        - item1
        - item2
    var2:
      list1:
        - item3
        - item1
        - item4
      list2:
        - itemA
        - itemB
  debug:
    msg: "{{ var1 | combine(var2, list_merge='append_rp') }}"
```

このタスクを実行すると以下の通り。  
リスト要素にどちらも`item1`が含まれるためこれが`var1`から削除され、`var2`の要素(`item3`,`item1`,`item4`)が末尾へ追加される。

```console
TASK [merge 2 dicts] *********************************************
ok: [localhost] => {
    "msg": {
        "list1": [
            "item2",
            "item3",
            "item1",
            "item4"
        ],
        "list2": [
            "itemA",
            "itemB"
        ]
    }
}
```

`prepend_rp`の動作は、↑と同じ条件でパラメタのみ変更して実行すると以下の通り。  
重複する`item1`が`var1`から削除される動作は同じで、`var2`の要素(`item3`,`item1`,`item4`)が先頭へ追加される。

```console
TASK [merge 2 dicts] *********************************************
ok: [localhost] => {
    "msg": {
        "list1": [
            "item3",
            "item1",
            "item4",
            "item2"
        ],
        "list2": [
            "itemA",
            "itemB"
        ]
    }
}
```

## パス処理

### パスの合成 (path_join)

`path_join`を使うと、リストの文字列をディレクトリセパレータで繋ぐ。

```yaml
- name: create repository url
  debug:
    msg:
    - "{{ (registry_url, registry_username, 'awx') | path_join }}"
```

```ini
registry_username = zaki-lknr
registry_url = ghcr.io
```

という変数が設定されていれば、実行結果は以下の通り。

```console
TASK [create repository url] ************************************
ok: [oci-g-a1-ubuntu] => 
  msg:
  - ghcr.io/zaki-lknr/awx
```

## 暗号化パスワード

- [How do I generate encrypted passwords for the user module?](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-encrypted-passwords-for-the-user-module)
- [Encrypting and checksumming strings and passwords](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#encrypting-and-checksumming-strings-and-passwords)

### password_hash

```yaml
password: "{{ password_plain_text | password_hash('sha512') }}"
```

デフォルトは`sha512`で暗号化される。  
<https://github.com/ansible/ansible/blob/v2.10.5/lib/ansible/plugins/filter/core.py#L266>

```python
def get_encrypted_password(password, hashtype='sha512', salt=None, salt_size=None, rounds=None):
```

saltの指定が無い場合はランダムになるため、実行のたびに結果の値は変化する。(つまり、このパスワードを設定したりすれば毎回`changed`になる)  
冪等にしたければ、saltに固定の値をセットする。

```yaml
password: "{{ password_plain_text | password_hash('sha512', 'hoge') }}"
```

- [Linux の UNIX パスワード認証について調べた - CUBE SUGAR CONTAINER](https://blog.amedama.jp/entry/linux-shadow-passwd)
- [【CentOS】/etc/shadowのハッシュ化パスワードについて - よくわからないエンジニア](https://www.unknownengineer.net/entry/2017/08/16/184537)

## base64

### エンコード

```yaml
- name: base64 enc
  vars:
    sample_text: zaki
  debug:
    msg: "{{ sample_text | b64encode }}"
```

↓

```console
ok: [localhost] => 
  msg: emFraQ==
```

### デコード

```yaml
- name: base64 dec
  vars:
    b64text: emFraQ==
  debug:
    msg: "{{ b64text | b64decode }}"
```

↓

```console
ok: [localhost] => 
  msg: zaki
```

## オブジェクトの型を調べる (type_debug)

任意のオブジェクトの型をチェックするには `type_debug` を使う。

```yml
- name: type debug
  debug:
    msg: "{{ item }}"
  vars:
    num_val: 1
    str_val: zzz
    list_val:
      - item1
      - item2
      - item3
    dict_val:
      key1: val1
      key2: val2
      key3: val3
  loop:
    - "{{ num_val | type_debug }}"
    - "{{ str_val | type_debug }}"
    - "{{ list_val | type_debug }}"
    - "{{ dict_val | type_debug }}"
```

結果

```console
TASK [type debug] ***********************************************************
ok: [localhost] => (item=int) => 
  msg: int
ok: [localhost] => (item=AnsibleUnicode) => 
  msg: AnsibleUnicode
ok: [localhost] => (item=list) => 
  msg: list
ok: [localhost] => (item=dict) => 
  msg: dict
```
