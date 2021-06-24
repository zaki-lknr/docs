# モジュール

## assert

```yaml
  - name: assert sample
    vars:
      hoge: "zzz"
      foo: "bar"
      baz: False
    ansible.builtin.assert:
      that:
        - "hoge is defined"
        - foo is defined
        - baz is defined and baz
      fail_msg: "'hoge','bar' must be defined, 'baz' must be true"
    register: assert_result
```

`that`で指定した各値がtrueでなければタスクが失敗する。  
変数ではなく文字列で指定するので、`:`を含む文字はYAMLのキーとしてparseされてしまうため、ダブルクォートなどで囲む必要がある。

```yaml
  - name: assert
    ansible.builtin.assert:
      that:
      - 'sample_text | regex_search("could not found: foobar")'
```

## lineinfile

```yaml
      lineinfile:
        path: lineinfile.txt
        regexp: '^port: 80'
        line: 'port: 8080'
```

lineinfile.txtファイルの中の`^port: 80`に __最後に__ マッチする行を`port: 8080`に変更。  
`port: 8080`が有る場合は、他に`^port: 80`にマッチしてもそれは対象外。

__最初に__ マッチする行を対象にする場合は`firstmatch: true`を指定。
