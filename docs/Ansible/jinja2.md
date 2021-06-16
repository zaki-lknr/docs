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
