# Jinja2テンプレート

増えたらToolsとかに移すかも

## 制御構文

### ループ

リストに対する`for`構文は、foreach式で配列要素をループ毎に処理できる

```jinja2
{% for item in array_value %}
...
{% endfor %}
```

`array_value`の配列要素が`item`にセットされてループ処理される。

#### ループインデックス

ループブロック内で`loop.index0`という変数参照でループインデックスを取り出せる(ゼロオリジン)

## コメント

```j2
{# comment #}
```

複数行もOK

```jinja2
{#
{% for item in list %}
{% endfor %}
#}
```

## lookupプラグイン実行

playbook等で書くときと同じ要領で記述可能

```j2
{{ lookup('pipe', 'date +%s') }}
```

## 指定部分へのフィルタの実行

### indent()

```jinja
{% filter indent(4, true) %}
{{ sample_string }}
{% endfilter %}
```

```yaml
---
- hosts: localhost
  gather_facts: false
  vars:
    sample_string: |
      int main() {
        return 0;
      }

  tasks:
    - name: j2 saple
      template:
        src: indent_sample.txt.j2
        dest: /var/tmp/indent_sample.txt
```

実行結果は以下の通り、スペース4文字分のインデントが入る。

```text
    int main() {
      return 0;
    }
```
