# 変数

## 接続設定

### ログインパスワード

[Connecting to hosts: behavioral inventory parameters](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters)

ネットワーク機器や公開鍵設定されてないサーバーOSへSSH接続の際のパスワードは`ansible_password`を使う。

```yaml
[dev-server]
192.168.0.211  ansible_user=developer ansible_password=deve1op!
```

※ ↑はplaintextで書いてるので、vaultで暗号化しましょう

### becomeパスワード

[Become connection variables](https://docs.ansible.com/ansible/latest/user_guide/become.html#become-connection-variables)

`become: true`を使う場合にパスワードが必要な場合、`-K (--ask-become-pass)`でパスワードプロンプトを表示するか、`ansible_become_password`で昇格用のパスワードを指定する。

```yaml
---
- hosts: linux
  gather_facts: true
  become: true
  vars:
    ansible_become_password: this_is_become_password!

  tasks:
    - name: enable sudo with no password (on RHEL)
      ansible.builtin.copy:
        ...
```

## マジック変数

[Special Variables — Ansible Documentation](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html)

### omit

省略したいパラメタにセットすると無視されるようになる。  
[[Ansible] 省略したいパラメタに変数指定せざるを得ない場合に使う変数omitとdefaultフィルタ - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/06/02/090251)

```yaml
  - name: create files by csv
    ansible.builtin.file:
      state: "{{ item.type }}"
      path: "{{ item.path }}"
      mode: "{{ item.mode | default(omit, true) }}"
      owner: "{{ item.owner | default(omit, true) }}"
      group: "{{ item.group | default(omit, true) }}"
    loop: "{{ res_input.list }}"
```

### role_path

現在実行中のロールのディレクトリパス。  
例えば `/path/to/ansible/roles/sample/tasks/main.yml`というディレクトリ構造のロールの中で `"{{ role_path }}"` を参照すると、`/path/to/ansible/roles/sample` を得られる。

ロールの中で参照できる変数なので、playbookで参照しても undefined エラーになる。

ロール内のtaskから`import_tasks`で読んだ先のファイルのtaskで参照する`role_path`であれば参照可能。  
(あくまで呼び出し元ロール基準のパスになる)
