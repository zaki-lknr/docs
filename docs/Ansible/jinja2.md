# Jinja2テンプレート

増えたらToolsとかに移すかも

[Templating (Jinja2) — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_templating.html)

## オンラインパーサー

[TTL255 J2Live - Live Jinja2 Parser](https://j2live.ttl255.com/)

## 変数

### 変数宣言

変数

```jinja2
{% set hogehoge = 'foobar' %}
```

配列

```jinja2
{% set array = [] %}
```

### 配列要素の追加

↑の`array`に要素を追加したい場合、Ansibleでは`array.append()`は使えるように見えて使えない。  
↓のように書く。

```jinja2
{% set _ = array.append('item') %} 
```

> これに `do` を追加して `{% do list.append(1) %}` と書けるようにする [Expression Statement](https://jinja.palletsprojects.com/en/latest/templates/#expression-statement) という拡張もあるらしいのですが Ansible では有効化されていませんので、面倒ですが `{% if list.append(1) %}{% endif %}` や `{% set _ = list.append(1) %}` のように他のステートメントの中で評価したい式を書く必要があります。
>
> [ほげめも: Ansible の Jinja2 を活用する](http://blog.keshi.org/hogememo/2015/12/07/exploiting-ansible-jinja2)

辞書をリストの要素として追加は普通にOK

```jinja2
{% set _ = host_list.append({'host': host, 'name': hostvars[host].tags.User }) %}
```

### 辞書

#### 値の参照

```jinja2
{{ foo.bar.baz }}
```

または

```jinja2
{{ foo['bar']['baz'] }}
```

後者の書式であればキー名に変数を使うこともできる。

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
