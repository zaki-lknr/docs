# モジュール

## setup

[ansible.builtin.setup – Gathers facts about remote hosts — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/setup_module.html)

facts変数を収集する。  
ad-hocで実行する方が多いかも？

```console
$ ansible -i inventory.ini all -m setup 
```

## set_fact

`vars`ディレクティブと異なりホスト変数を定義するので、セット後の以降のタスク・プレイでも変数参照できる。

```yaml
- name: define variable
  ansible.builtin.set_fact:
    _state_val: "{{ foobar | default(true) }}"
```

`vars`ディレクティブと異なり定義した変数を即参照することはできない。

```yaml
# ダメな例
- name: define variable
  ansible.builtin.set_fact:
    _state_val: "{{ foobar | default(true) }}"
    _mode: "{{ _state_val | ternary(...) }}"
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

## uri

HTTPをしゃべる。  
以下はAWX/AAPで通知テストをkickするRESTを叩くタスク。  
basic認証+HTTPSでSSL検証無効の設定で、レスポンスコードが`200`or`201`or`202`を期待している。

```yaml
  ansible.builtin.uri:
    method: POST
    url: "https://{{ aap_address }}/api/v2/notification_templates/{{ notify_id }}/test/"
    url_username: "{{ username }}"
    url_password: "{{ password }}"
    validate_certs: false
    force_basic_auth: true
    status_code: [200, 201, 202]
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

[ansible.builtin.replace – Replace all instances of a particular string in a file using a back-referenced regular expression — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/replace_module.html)

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

## redhat_subscription

[community.general.redhat_subscription – Manage registration and subscriptions to RHSM using the subscription-manager command — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/community/general/redhat_subscription_module.html)

RHELのサブスクリプション割り当てを行う。
開発者ライセンスならプールIDは1個しかないので

```yaml
    - name: subscription
      community.general.redhat_subscription:
        state: present
        username: "{{ rh_username }}"
        password: "{{ rh_password }}"
        auto_attach: true
```

ですべて完了する。

[制限ネットワーク環境のRHEL7でproxy経由でサブスクリプション登録とDockerインストール - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/09/05/160946)

これでいうと`subscription-manager register`して`subscription-manager attach --pool=****`まで終わった状態になる。

## alternatives

[community.general.alternatives – Manages alternative programs for common commands — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/community/general/alternatives_module.html)

`community.general.alternatives`で`alternatives`コマンドを使ったコマンド切り替え設定を行う。

```yaml
- name: alternatives python
  community.general.alternatives:
    name: python
    link: /usr/bin/python
    path: /usr/bin/python3
  become: true
```

これで、`/usr/bin/python` -> `/etc/alternatives/python` -> `/usr/bin/python3`へのリンクが`python`という名前で登録される。  
コマンドとしては以下と同等。  
(`priority`はデフォルト50)

```console
# update-alternatives --install /usr/bin/python python /usr/bin/python3 50
```

## パッケージ

### dnf

CentOS7(yum環境)に使える？ => NG

```
fatal: [cloud-dev]: FAILED! => changed=false 
  msg: Could not import the dnf python module using /usr/bin/python (2.7.5 (default, Nov 16 2020, 22:23:17) [GCC 4.8.5 20150623 (Red Hat 4.8.5-44)]). Please install `python3-dnf` or `python2-dnf` package or ensure you have specified the correct ansible_python_interpreter. (attempted ['/usr/libexec/platform-python', '/usr/bin/python3', '/usr/bin/python2', '/usr/bin/python'])
  results: []
```

ただし、CentOS7で`sudo yum install python2-dnf`を入れると動く。  
dnfモジュールでパッケージインストールし、CentOS7上で`rpm -qa`とかしても確認できる。

### yum

Fedora34 / RHEL8に使える？ => OK

### apt_repository

```yaml
- name: add apt repository
  ansible.builtin.apt_repository:
    repo: deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu focal stable
    filename: docker
```

これで以下のファイルが作成される。

```console
$ cat /etc/apt/sources.list.d/docker.list
deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu focal stable
```

このとき、`/etc/apt/sources.list.d/docker.list`以外のファイルですでに同じ設定がある場合は`docker.list`は作成されずに`ok`となる。

## ファイル

### state

#### file

対象ファイルがない場合はエラーになる。  
逆手にとってファイル存在チェックに使える(その場合エラーは無視するようにする必要はある)

以下は`/var/tmp/ansible-file`が無い場合に最初のタスクが失敗(`ignore_errors`により無視)し、次のタスクで`file not exist`が出力される。ファイルがある場合は最初のタスクは成功し、続くタスクは`when`の条件を満たさないためスキップされる。

```yaml
- name: exist file?
  file:
    path: /var/tmp/ansible-file
    state: file
  register: result
  ignore_errors: true

- debug:
    msg: file not exist
  when: result is failed
```

`failed_when`を使うなら以下

```yaml
  - name: dounaru
    file:
      path: /var/tmp/ansible-file
      state: file
    register: result
    failed_when: false

  - debug:
      msg: file not exist
    when: result.state == "absent"
```

### モード

```yaml
- name: create file
  file:
    path: /var/tmp/file_permission
    mode: "0755"
    state: directory
```

これなら

```console
$ ll -d /var/tmp/file_permission/
drwxr-xr-x. 2 zaki zaki 6  7月 31 10:27 /var/tmp/file_permission/
```

| mode           | 実体           |
| -------------- | ------------ |
| `"0755"` (str) | `drwxr-xr-x`  |
| `0755` (int)   | `drwxr-xr-x`  |
| `"755"` (str)  | `drwxr-xr-x`  |
| `755` (int)    | `d-wxrw--wt` |

`755`にすると、10進数の755入力を8進数と解釈し`1363`としてモード設定される動作になる。
モード設定は8進数の数値か文字列型の数値が入力なのと、先頭ゼロの数値は8進数としてYAMLが解釈するので、先頭ゼロを付けずにintの数値を指定すると想定外の動作になる。

## fetch

ターゲットノードのファイルをコントロールノードへコピーする。  
ディレクトリは不可。(`src`のパスがディレクトリの場合は失敗する)

```console
fatal: [fedora-node]: FAILED! => changed=false 
  file: /home/zaki/src/docker-images/
  msg: remote file is a directory, fetch cannot work on directories
```

[ドキュメントのsrcパラメタの項](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/fetch_module.html#parameter-src)にも記載あり。

> This must be a file, not a directory.

fetchモジュールの使い方全般ははてブに書いた。  
[[Ansible] fetchモジュールを使ってリモートのファイルを実行ノードへ転送・集約する - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/12/13/060649)

## slurp

ターゲットノード上のファイルの中身を取得する。  
fetchと異なりファイルシステム上へ保存せずにオンメモリ処理。  
base64エンコードされているのででコードして使用する。

```yaml
  - name: get private key
    ansible.builtin.slurp:
      src: /home/ec2-user/.ssh/id_rsa
    register: ssh_private_key

  - name: create repository credential on awx
    awx.awx.credential:
      name: git-repository-credential
      organization: Default
      credential_type: Source Control
      inputs:
        username: ec2-user
        ssh_key_data: "{{ ssh_private_key.content | b64decode }}"
```

## make

`/path/to/awx-operator`ディレクトリ上で`make deploy IMG=zakihmkc/awx-operator:0.17.0`を実行するタスクであれば以下の通り。

```yaml
- name: deploy awx-operator
  community.general.make:
    chdir: /path/to/awx-operator
    target: deploy
    params:
      IMG: zakihmkc/awx-operator:0.17.0
```

ターゲットファイル作成済みで`make`が何も処理しない場合は、実行ステータスは`ok`となる。

## meta

### 再接続

たとえば、OSユーザーのグループを更新した場合、通常であればログインし直さなければ変更が反映されない。  
Ansibleも同様で、タスクでグループの更新を行っても、後続のタスクは接続が維持されているため、更新されたグループの状態でタスクが実行されない。  
この場合、[metaモジュール](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/meta_module.html)の`reset_connection`を使って再接続を行い、変更を反映して後続のタスクを実行できる。

```yaml
  - name: グループを変更するタスク
    ...

  - name: refresh connection
    ansible.builtin.meta: reset_connection

  - name: 変更したグループ前提のタスク
    ...
```

### playの終了

プログラムにおける`exit(0)`みたいなやつ。  
任意のタイミングでplayを正常終了させることができる。

```yaml
  tasks:
  - name: task1
    debug:
      msg: task1

  - name: task2
    debug:
      msg: task2

  - name: end play
    ansible.builtin.meta: end_play

  - name: task3
    debug:
      msg: task3
```

このplaybookの内容でAnsible実行すると、以下の通りtask3は実行されずに終了する。

```console
PLAY [localhost] ****************************************************************

TASK [task1] ********************************************************************
ok: [localhost] => 
  msg: task1

TASK [task2] ********************************************************************
ok: [localhost] => 
  msg: task2

TASK [end play] *****************************************************************

PLAY RECAP **********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

### インベントリの再読み込み

ダイナミックインベントリでホスト情報を再収集したい場合、など。

```yaml
- name: refresh inventory
  ansible.builtin.meta: refresh_inventory
```

[[Ansible] 任意のタイミングでダイナミックインベントリのホスト情報収集を行う - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2022/11/09/203145)

## fail

指定のメッセージを出力してplayを失敗させる

```yaml
- ansible.builtin.fail:
    msg: failed task
```

## Docker

### push image

`docker_image`を使用しパラメタに`push: true`を指定。

```yaml
- name: push container image
  community.docker.docker_image:
    name: quay.io/ansible/awx-ee
    repository: zakihmkc/awx-ee:0.6.0
    push: true
    source: local
```

このタスクの場合は、ローカルにある`quay.io/ansible/awx-ee`イメージをDocker Hubへ`zakihmkc/awx-ee:0.6.0`として`push`する。  
このとき、`zakihmkc/awx-ee:0.6.0`が既にローカルにある場合はtagの上書きが発生しないため、pushされない。ここでpushしたい場合は、tag設定を上書きする`force_tag: true`も追加する。

## git

```yaml
  ansible.builtin.git:
    repo: https://github.com/zaki-lknr/awx_build_and_deploy.git
    version: feature/awx-ver-21.13.0
    dest: /var/tmp/work-dir
```

destのディレクトリは事前に作成しておく。  
GitモジュールはCLIの`git clone`と異なり、上の例でいうと`awx_build_and_deploy`サブディレクトリは作成されない。

## ssh

### openssh_keypair

ssh key pair作成

```yaml
    community.crypto.openssh_keypair:
      path: "{{ ansible_env.HOME }}/.ssh/id_rsa"
      size: 4096  # default
      type: rsa   # default
    register: ssh_keypair_result
```

### authorized_key

`authorized_keys`への公開鍵登録

```yaml
    ansible.posix.authorized_key:
      user: "{{ ansible_env.USER }}"
      key: "{{ ssh_keypair_result.public_key }}"
      # key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
    delegate_to: localhost
    run_once: true
```

## pip

venvを作り指定のPythonパッケージ入れる。  
pipは最新にしつつ、バージョン指定のパッケージも入れるならこんな感じ。

```yaml
    - name: create venv
      ansible.builtin.pip:
        name:
          - pip
          - ansible-core==2.18.0
        state: latest
        virtualenv: /path/to/venv/ansible
        virtualenv_command: python3 -m venv
```

ターゲットノードのこのvenvを使ってAnsible実行するには以下。

[[Ansible] ターゲットノードのvenvのPythonを指定して実行する (interpreter_python / ansible_python_interpreter / PYTHONPATH) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/05/26/230432)

この辺の設定をどこかに入れればOK

```yaml
ansible_python_interpreter: /path/to/venv/ansible/bin/python
```

## pause

sleepする

```yaml
  - name: sleep
    ansible.builtin.pause:
      seconds: 10
```

単純なsleepでなく、最大で指定時間だけwaitがかかり、その間に`Ctrl-c`押下で「即再開 or 処理中断」の選択が可能になる。  
`a`で中断した場合はそこで処理が終了する。

```console
TASK [sleep] *************************************************************************
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
Press 'C' to continue the play or 'A' to abort 
fatal: [localhost]: FAILED! => 
  msg: user requested abort!
```

## wait_for

処理対象ホストの80/TCPがAnsible実行ホストから接続可能になるまで最大300秒waitするタスク

```yaml
- ansible.builtin.wait_for:
    host: "{{ ansible_host }}"
    port: 80
    delay: 5
    state: started
    timeout: 300
  delegate_to: localhost
```

逆に終了処理の確認などで接続不可になるまでwaitするのであれば、`state: stopped`を指定する。

## debug

`msg`を使えば(変数を含め)任意の文字列をプリントする。  
変数だけで良ければ`var`を使う方が楽。

```yaml
---
- hosts: localhost
  gather_facts: false
  vars:
    val1: 1234

  tasks:
    - debug:
        var: val1
```

ただし`var`の場合はリスト出力は不可

```yaml
debug:
  var:
  - val1
  - val2
  # 不可
```
