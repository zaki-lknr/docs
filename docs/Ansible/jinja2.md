# Jinja2テンプレート

増えたらToolsとかに移すかも

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
