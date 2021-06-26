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

## includeとimport

- [Re-using Ansible artifacts — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse.html)
- [include および import — Ansible Documentation](https://docs.ansible.com/ansible/2.9_ja/user_guide/playbooks_reuse_includes.html)
- [OSSはアルミニウムの翼で飛ぶ: Ansible: include の代わりに使う import_xxx, include_yyy とは](http://aikotobaha.blogspot.com/2017/12/ansible-include-importxxx-includeyyy.html)

とりあえずリンク
