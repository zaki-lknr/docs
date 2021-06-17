# ディレクティブ

## ループ

### ループ変数のカスタム

`loop_control.loop_var`で指定できる。

```yaml
    - name: loop
      debug:
        msg: "{{ zzz }}"
      loop:
        - foo
        - bar
        - baz
        - qux
      loop_control:
        loop_var: zzz
```

`{{ item }}`は使用不可となる。
