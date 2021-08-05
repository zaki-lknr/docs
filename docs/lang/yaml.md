# YAML

## tools

[Online YAML Parser](https://yaml-online-parser.appspot.com/)


## 複数行

[[YAML] 複数行テキストの行頭にインデントを設定するには (Ansible/Kubernetes) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/07/17/220311)

### インデント

```yaml
block: |
  hoge
  foo
  bar
```

この場合の`block`の値は

```text
hoge
foo
bar
```

となる。  
これはインデント量を(各行同じ量ずつ)変化させても同じ。

`block`の値の文字列内でインデントが欲しい場合は

```yaml
block: |2
      hoge
      foo
      bar
```

と書くことで、「2文字分はYAMLの書式としてのインデント」と解釈され、残りのスペースは文字列部分として解釈される。
