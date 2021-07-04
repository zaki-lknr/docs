# モジュール

## setup

[ansible.builtin.setup – Gathers facts about remote hosts — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/setup_module.html)

facts変数を収集する。  
ad-hocで実行する方が多いかも？

```console
$ ansible -i inventory.ini all -m setup 
```

## assert

[ansible.builtin.assert – Asserts given expressions are true — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/assert_module.html)

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

[ansible.builtin.lineinfile – Manage lines in text files — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html)

```yaml
      lineinfile:
        path: lineinfile.txt
        regexp: '^port: 80'
        line: 'port: 8080'
```

lineinfile.txtファイルの中の`^port: 80`に __最後に__ マッチする行を`port: 8080`に変更。  
`port: 8080`が有る場合は、他に`^port: 80`にマッチしてもそれは対象外。

__最初に__ マッチする行を対象にする場合は`firstmatch: true`を指定。

ファイル内にマッチする行が見つからなかった場合は、デフォルトではファイル末尾に追記される。  
ファイル末尾以外に追記したい場合は、`insertafter`または`insertbefore`を使うことで、指定行直下・または直前に挿入できる。
`insertafter`と`insertbefore`は指定できるのはどちらか片方。(両方同時指定は不可)

## blockinfile

[ansible.builtin.blockinfile – Insert/update/remove a text block surrounded by marker lines — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/blockinfile_module.html)

マーカー行が複数ある場合は、「ファイル内一番末尾の開始マーカー・終了マーカー」の間が処理対象になった。(ansible 2.10 / ansible 2.11で確認)

```text
# /share/www configure begin
1
# /share/www configure end

# /share/www configure begin
2
# /share/www configure end

<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

# /share/www configure begin
3
# /share/www configure end
```

こんな入力ファイルに対して

```yaml
    - name: blockinfile sample
      blockinfile:
        path: blockinfile-test.txt
        marker_begin: begin
        marker_end: end
        marker: '# /share/www configure {mark}'
        block: |
          ...
```

を実行すると、更新されるのは `3` の部分のみ。  
(複数のbegin行がある場合は最後のものが使用される)

入力ファイルが以下の場合は、`1`から`3`までの範囲が更新される。  
(end行も複数ある場合は最後のものが使用される)

```text
# /share/www configure begin
1
# /share/www configure end

2
# /share/www configure end

<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

3
# /share/www configure end
```

---

これなら一度で確かめられた。  
↓のファイルなら、blockinfileが更新するのは`3`から`5`まで。

```text
# /share/www configure begin
1

# /share/www configure begin
2

# /share/www configure begin
3
# /share/www configure end

<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

4
# /share/www configure end

5
# /share/www configure end
```

## replace

ansible.builtin.replace – Replace all instances of a particular string in a file using a back-referenced regular expression — Ansible Documentation
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/replace_module.html

`after`/`before`で「ここからここまでの間」を指定できる。

```yaml
    - name: replace sample
      replace:
        path: duplicate-test.txt
        regexp: ^[0-9]+$
        replace: zzz
        after: '# /share/www configure begin'
        before: '# /share/www configure end'
```

入力ファイルが↓のように複数マッチする場合は

```text
# /share/www configure begin
1

# /share/www configure begin
2

# /share/www configure begin
3
# /share/www configure end

<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

4
# /share/www configure end

5
# /share/www configure end
```

`1`から`3`までが対象だった。(`after`も`before`も最初にマッチした部分が対象)
