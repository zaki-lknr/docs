# 変数

- [Using Variables — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)
- [Special Variables — Ansible Documentation](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html)
- [Discovering variables: facts and magic variables — Ansible Documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html)

## 定義の優先順位

- [Understanding variable precedence](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#understanding-variable-precedence)
- [Ansible 変数の優先順位と書き方をまとめてみた - Qiita](https://qiita.com/answer_d/items/b8a87aff8762527fb319)

## 接続設定

### SSH関連設定

同一playbookで、playによって接続用アカウントを変えたいときなど。

- ansible_user
- ansible_ssh_private_key_file

たとえば、最初は管理用アカウントで接続してユーザー作成など行って、次のplayでは作成したユーザーで接続できることを確認しつつアプリのデプロイを行う、などなど。  
コマンドラインオプションでは、それぞれ`-u`と`--private-key`で指定可能。

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

## ノードのファクト変数

```yaml
gather_facts: true
```

全部取ると重いけど、`gather_subset`を指定するとサブセットだけ取れる。

```yaml
---
- hosts: all
  gather_facts: true
  gather_subset:
    - network

  tasks:
  - debug:
      msg: "{{ ansible_facts }}"
```

これで`wc -l`すると約375行。  
`gather_subset`の指定が無いと926行。

`ansible all -m setup | wc -l`だと916行。  
`ansible -i inventory all -m setup -a "gather_subset=network" | wc -l`だと365行。

OS情報程度であれば`min`で取れる。  
インタフェース毎のネットワーク設定は`network`も必要っぽい。

### 環境変数

`gather_subset: min`で確認。  
`become`なしの場合。

```json
        "ansible_env": {
            "DBUS_SESSION_BUS_ADDRESS": "unix:path=/run/user/1001/bus",
            "HOME": "/home/ubuntu",
            "LANG": "C.UTF-8",
            "LOGNAME": "ubuntu",
            "MOTD_SHOWN": "pam",
            "PATH": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin",
            "PWD": "/home/ubuntu",
            "SHELL": "/bin/bash",
            "SHLVL": "0",
            "SSH_CLIENT": "****",
            "SSH_CONNECTION": "****",
            "SSH_TTY": "/dev/pts/1",
            "TERM": "xterm-256color",
            "USER": "ubuntu",
            "XDG_RUNTIME_DIR": "/run/user/1001",
            "XDG_SESSION_CLASS": "user",
            "XDG_SESSION_ID": "29",
            "XDG_SESSION_TYPE": "tty",
            "_": "/bin/sh"
        },
```

`become`ありの場合。

```json
        "ansible_env": {
            "HOME": "/root",
            "LANG": "C.UTF-8",
            "LOGNAME": "root",
            "MAIL": "/var/mail/root",
            "PATH": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin",
            "PWD": "/home/ubuntu",
            "SHELL": "/bin/bash",
            "SUDO_COMMAND": "/bin/sh -c echo BECOME-SUCCESS-****** ; /usr/bin/python3 /home/ubuntu/.ansible/tmp/ansible-tmp-****/AnsiballZ_setup.py",
            "SUDO_GID": "1001",
            "SUDO_UID": "1001",
            "SUDO_USER": "ubuntu",
            "TERM": "xterm-256color",
            "USER": "root"
        },
```

昇格前のユーザー情報は`SUDO_USER`と`PWD`を見るしか無いかな？
