# モジュール

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
