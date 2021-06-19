# Lookupプラグイン

[Lookup Plugins — Ansible Documentation](https://docs.ansible.com/ansible/latest/plugins/lookup.html)

Jinja2テンプレートの書式と連携して、ファイル参照やコマンド実行など外部リソースを利用できる。  
評価されるのはコントロールノード上であって、ターゲットノードのファイルやコマンド実行ではないことに注意。

taskを1個消費せずにインラインでファイルの内容をロードしたりコマンド実行したりできるので便利

## ファイル

```yaml
  - name: load ssh public key
    vars:
      pubkey: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
    debug:
      msg: "{{ pubkey }}"
```

`ansible-playbook`の実行ユーザの`~/.ssh/id_rsa.pub`の内容を読み込んだ内容が`pubkey`にセットされる。

## 環境変数

```yaml
  - name: env lookup
    debug:
      msg: "{{ lookup('env', 'HOSTNAME') }}"
```

環境変数`HOSTNAME`を取得する。  
コントロールノードでの評価結果になるので、どのターゲットノードのtaskの実行でも、コントロールノードのホスト名になる。

ターゲットノードの情報が欲しい場合は、`gather_facts: true`でfact情報を参照する。  
[ansible-sample/env.yml at master · zaki-lknr/ansible-sample](https://github.com/zaki-lknr/ansible-sample/blob/master/lookup/env.yml)

## コマンド実行

```yaml
  - name: get date
    vars:
      current_date: "{{ lookup('pipe', 'date +%Y/%m/%d_%H:%M:%S') }}"
      # https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pipe_lookup.html
      # pipeを使うとコマンド実行結果を取得できる

    debug:
      msg:
      - "{{ current_date }}"
```

## パスワード文字列生成

[ansible.builtin.password – retrieve or generate a random password, stored in a file — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/password_lookup.html)

```yaml
  tasks:
    - name: create password
      debug:
        msg: "{{ lookup('password', '/var/tmp/ansible-work/password') }}"
```

実行結果は

```
TASK [create password] *************************
ok: [localhost] => 
  msg: Uk.9vigyP2IlQn2q4620
```

`/var/tmp/ansible-work/password`の中身は

```console
$ cat /var/tmp/ansible-work/password 
Uk.9vigyP2IlQn2q4620
```

がファイルがなければ新規に作成される。
ファイルが既に存在する場合は「既存ファイル内のパスワード」が返る。(ので、再実行するとファイルの内容が取得される)

実行のたびに異なるパスワードを生成するのであれば、`/dev/null`を指定する

```yaml
- hosts: localhost
  gather_facts: false
  vars:
    create_password: "{{ lookup('password', '/dev/null') }}"

  tasks:
    - name: create password (1)
      debug:
        msg: "{{ create_password }}"

    - name: create password (2)
      debug:
        msg: "{{ create_password }}"
```

実行結果

```
TASK [create password] **************************************************
ok: [localhost] => 
  msg: sw:oDzMrVtdxy0aOdsEw

TASK [create password] **************************************************
ok: [localhost] => 
  msg: 9imT.elnUhq1.GpALpX3
```

これだと生成したパスワードを見ることができないので、`set_fact`で一度作成済みにして、それを使うようにすれば、「パスワード設定」と「設定したパスワードのストア」を自前で管理できる。

```
    - name: create password
      set_fact:
        password: "{{ create_password }}"

    - name: print password
      debug:
        msg: "{{ password }}"
```

`-v`つけて実行すれば以下の通り。

```
TASK [create password] **************************************************
ok: [localhost] => changed=false 
  ansible_facts:
    password: -StMaxh9fmrGIkMYj_qJ

TASK [print password] **************************************************
ok: [localhost] => 
  msg: -StMaxh9fmrGIkMYj_qJ
```
