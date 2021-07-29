# フィルタ

ドキュメントはAnsibleとJinja2両方チェックする。

- [Using filters to manipulate data — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)
- [Template Designer Documentation — Jinja Documentation (3.1.x)](https://jinja.palletsprojects.com/en/latest/templates/#list-of-builtin-filters)

コレクションによっては個別のドキュメントもある。

- [community.general Filter Guide — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/community/general/docsite/filter_guide.html)

実装はこのあたり

- ansible.builtin
    - [ansible/core.py at devel · ansible/ansible](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/filter/core.py)
    - core以外のものは同じディレクトリの別ソースにあるかも
- jinja2
    - [jinja/filters.py at main · pallets/jinja](https://github.com/pallets/jinja/blob/main/src/jinja2/filters.py)

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

[[Ansible / Jinja2] select / selectattr を使った配列と辞書のフィルタリング - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/02/18/000228)

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

## 暗号化パスワード

- [How do I generate encrypted passwords for the user module?](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-encrypted-passwords-for-the-user-module)
- [Encrypting and checksumming strings and passwords](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#encrypting-and-checksumming-strings-and-passwords)

### password_hash

```yaml
password: "{{ password_plain_text | password_hash('sha512') }}"
```

デフォルトは`sha512`で暗号化される。
saltの指定が無い場合はランダムになるため、実行のたびに結果の値は変化する。(つまり、このパスワードを設定したりすれば毎回`changed`になる)
冪等にしたければ、saltに固定の値をセットする。

```yaml
password: "{{ password_plain_text | password_hash('sha512', 'hoge') }}"
```
