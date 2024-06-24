# プレイブック

これCLIとかコマンドってタイトルの方がいいか

## 共通

### -e | --extra-vars

スペースを含む値を与える場合

```console
$ ansible-playbook ... -e 'key="foo bar baz"'
```

あるいはJSON形式で。(JSONだと辞書もリストもいける)

```console
$ ansible-playbook ... -e '{key: "foo bar baz"}'
```

変数ファイルを指定する場合

```yaml
---
netbox_url: http://netbox.example.org/
netbox_token: hogehoge
```

こんなファイルを`netbox-vars.yml`として用意しておき、`ansible-playbook`実行時に

```console
$ ansible-playbook ... -e @/path/to/netbox-vars.yml
```

で、このファイルで定義した変数を実行時にextra varsとして参照できる。

### -l | --limit

playbookの`hosts`で指定してあるホストグループの内、特定のホストだけに処理したい場合に使う。

```console
$ ansible-playbook ... -l host1
```

こうすると、playbookで例えば`hosts: servers`と指定されており、serversグループに `host1`, `host2`, `host3` が有ったとしても、この`ansible-playbook`実行のタスクは `host1` のみが処理対象で実行される。

開発やデバッグのお供に。

### --connection-password-file

リモートへのログインに使用するパスワードが書かれたファイルを指定し、対話的処理を回避する。

```console
ansible-playbook playbook.yml -i inventory.ini --connection-password-file password.txt 
```

指定するファイルはパスワードのみがtext/plainで書かれている。

## ansible-playbook コマンド

### --check

いわゆるDry Runで実行する。  
checkに対応している`copy`モジュールの場合は以下の通り。

```yaml
  - name: command sample
    copy:
      src: /home/zaki/tmp.yml
      dest: /var/tmp
      mode: '0644'
    register: result
```

このplaybook、コピー先が存在しない状態で、実際に動かすと`changed`になる状態で`--check`付けて実行すると

```console
(ansible4) [zaki@cloud-dev shell (master)]$ ls /var/tmp/tmp.yml 
ls: /var/tmp/tmp.yml にアクセスできません: そのようなファイルやディレクトリはありません
(ansible4) [zaki@cloud-dev shell (master)]$ ansible-playbook copy.yml --check

PLAY [localhost] ********************

TASK [command sample] ***************
changed: [localhost]

TASK [print result] *****************
ok: [localhost] => 
  msg:
    changed: true
    diff: []
    failed: false
:
:
(ansible4) [zaki@cloud-dev shell (master)]$ ls /var/tmp/tmp.yml 
ls: /var/tmp/tmp.yml にアクセスできません: そのようなファイルやディレクトリはありません
```

このとおり`changed`として処理され、resultもchangedになる。  
ファイルが存在して`ok`となる状態で`--check`で実行すると、以下の通り。

```console
(ansible4) [zaki@cloud-dev shell (master)]$ ls /var/tmp/tmp.yml
/var/tmp/tmp.yml
(ansible4) [zaki@cloud-dev shell (master)]$ ansible-playbook copy.yml --check
PLAY [localhost] ********************

TASK [command sample] ***************
ok: [localhost]

TASK [print result] *****************
ok: [localhost] => 
  msg:
    changed: false
    checksum: e4012c7ec05eb3bd3d27774fa73b772426c02d74
    dest: /var/tmp/tmp.yml
    diff:
      after:
        path: /var/tmp/tmp.yml
      before:
        path: /var/tmp/tmp.yml
    failed: false
    gid: 1000
    group: zaki
    mode: '0644'
    owner: zaki
    path: /var/tmp/tmp.yml
    secontext: unconfined_u:object_r:user_home_t:s0
    size: 5447
    state: file
    uid: 1000
```

タスク単位でチェックモードを指定する`check_mode`パラメタについては[directives](/Ansible/directives/#check-mode)参照。

### --list-tasks

同時に指定する`--tags`などの実行条件を踏まえて、どのプレイとタスクが実行対象となるかをリストアップする。dry runのもう1段階手前な感じ。

```console
$ ansible-playbook setup.yml --list-tasks

playbook: setup.yml

  play #1 (rhel): rhel  TAGS: []
    tasks:
      subscription      TAGS: [subscription]
      install packages  TAGS: [packages]

  play #2 (rhel): rhel  TAGS: []
    tasks:
      ssh user config   TAGS: [ssh]
      git config        TAGS: [git]
      get git-prompt.sh TAGS: [git]
      add prompt with git config        TAGS: [git]
      add source gitprompt      TAGS: [git]
      create venv       TAGS: [python]
      clone repository (linux)  TAGS: [repository]
      clone repository (container)      TAGS: [repository]
```

`--tags`指定ありの場合

```console
$ ansible-playbook setup.yml --list-tasks --tags git

playbook: setup.yml

  play #1 (rhel): rhel  TAGS: []
    tasks:

  play #2 (rhel): rhel  TAGS: []
    tasks:
      git config        TAGS: [git]
      get git-prompt.sh TAGS: [git]
      add prompt with git config        TAGS: [git]
      add source gitprompt      TAGS: [git]
```

### --start-at

指定タスクから処理を開始する。開発・デバッグのお供に。

```yaml
---
- hosts: localhost
  gather_facts: false

  tasks:
  - name: exec 1st task
    debug:
      msg: "hello world"

  - name: exec 2nd task
    debug:
      msg: "hello worlds"

  - name: exec 3rd task
    debug:
      msg: "hello worlds!!"
```

```console
$ ansible-playbook sample.yml --start-at "exec 2nd task"
```

実行結果

```console
PLAY [localhost] *****************************************************************************

TASK [exec 2nd task] *************************************************************************
ok: [localhost] => 
  msg: hello worlds

TASK [exec 3rd task] *************************************************************************
ok: [localhost] => 
  msg: hello worlds!!
```

roleの場合は、ロール名含めて指定すればよい。(実行時の`TASK [***]`の`***`の部分を指定する)

```console
$ ansible-playbook -i inventory-oci-l-dev.ini playbook.yml -v
:
:
TASK [build_awx/awx-ee : clone repository] ***************************************************
fatal: [oci-l-a1-ubuntu]: FAILED! => {"before": "54de4047be742783f69678f942a515e991c097ab", "changed": false, "msg": "Local modifications exist in repository (force=no)."}

PLAY RECAP ***********************************************************************************
oci-l-a1-ubuntu            : ok=33   changed=5    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0   

:
:
$ ansible-playbook -i inventory-oci-l-dev.ini playbook.yml -v --start-at "build_awx/awx-ee : create working directory"
```

# sample

## playbook

```yaml
- hosts: localhost
  gather_facts: false

  tasks:
  - name: hello
    debug:
      msg: hello world
```

# inventory

```ini
[server]
192.168.0.1
```
