# プレイブック

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
