# フィルタ

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