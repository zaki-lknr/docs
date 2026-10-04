# ディレクティブ

[Playbook Keywords — Ansible Documentation](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html)

## ループ

### range()を使って指定回数ループ

`host0`から`host9`まで作りたい場合。

```yaml
- name: loop
  awx.awx.host:
    name: "host{{ item }}"
    inventory: inventory
    enable: true
  loop: "{{ range(0, 10) }}"
```

### ループとblockは併用不可

```yaml
  tasks:
    - name: block with loop
      loop: "{{ item_list }}"
      block:
        :
```

こういうのは無理

```console
ERROR! 'loop' is not a valid attribute for a Block
```

[Blocks — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_blocks.html)

> Most of what you can apply to a single task (with the exception of loops) can be applied at the block level, so blocks make it much easier to set data or directives common to the tasks.

### ループとregisterの併用

- [[Ansible] loopとモジュール実行の結果を保持するregisterを併用する - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/04/06/190124)
- [Registering variables with a loop](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html#registering-variables-with-a-loop)

```yaml
    - name: exec command
      ansible.builtin.command: "{{ item }}"
      loop:
        - hostname
        - pwd
        - ls /opt
      register: result_command

    - name: print result
      debug:
        msg: '{{ result_command }}'
```

を実行すると、以下のようになる。

```yaml
ok: [localhost] => 
  msg:
    changed: true
    msg: All items completed
    results:
    - ansible_loop_var: item
      changed: true
      cmd:
      - hostname
      delta: '0:00:00.001672'
      end: '2021-04-06 18:54:28.461799'
      failed: false
      invocation:
        module_args:
          ...
      item: hostname
      rc: 0
      start: '2021-04-06 18:54:28.460127'
      stderr: ''
      stderr_lines: []
      stdout: cloud-dev
      stdout_lines:
      - cloud-dev
    - ansible_loop_var: item
      changed: true
      cmd:
      - pwd
      delta: '0:00:00.001590'
      end: '2021-04-06 18:54:28.630725'
      failed: false
      invocation:
        module_args:
          ...
      item: pwd
      rc: 0
      start: '2021-04-06 18:54:28.629135'
      stderr: ''
      stderr_lines: []
      stdout: /home/zaki/src/ansible-sample/file
      stdout_lines:
      - /home/zaki/src/ansible-sample/file
    - ansible_loop_var: item
      changed: true
      cmd:
      - ls
      - /opt
      delta: '0:00:00.001935'
      end: '2021-04-06 18:54:28.798976'
      failed: false
      invocation:
        module_args:
          ...
      item: ls /opt
      rc: 0
      start: '2021-04-06 18:54:28.797041'
      stderr: ''
      stderr_lines: []
      stdout: |-
        cni
        containerd
      stdout_lines:
      - cni
      - containerd
```

要は、`${registerで指定した変数名}.results[...]`という配列になる。

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

### ループインデックスの参照

タスク内でループインデックスを参照したい場合は、`extended: true`を付加すれば参照できる。

```yaml
- name: loop index sample
  debug:
    msg:
    - "{{ item }}"
    - "{{ ansible_loop.index }}"
    - "{{ ansible_loop.index0 }}"
  loop:
    - foo
    - bar
    - baz
  loop_control:
    extended: true
```

実行結果は以下の通り。

```console
ok: [localhost] => (item=foo) => 
  msg:
  - foo
  - '1'
  - '0'
ok: [localhost] => (item=bar) => 
  msg:
  - bar
  - '2'
  - '1'
ok: [localhost] => (item=baz) => 
  msg:
  - baz
  - '3'
  - '2'
```

使用可能な変数はこちら→ [Extended loop variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html#extended-loop-variables)

### ループとinclude併用時にループに関係ないタスクを1回だけ実行することは…

`run_once`使って出来ないか試したけど予想通り無効。  
たぶん無理なので、そういうタスクはループの外で実装しましょう。

```yaml
  - name: include with loop
    include_tasks: dup-loop-included.yml
    loop: "{{ users }}"
```

```yaml
- name: 1回だけ実行したいタスク
  debug:
    msg: zzz
  run_once: true
```

このタスクは`users`の要素のループごとに当然毎回実行される。  
ループ内で毎回実行される必要がないのであれば、includeの外で実行しましょう。

## when

### 基本

```yaml
tasks:
- module:
  vars:
    cond: (bool)
  when:
    - cond
```

`when`部分は変数参照の `"{{ values }}"` の表記は不要で、bool型の条件式・変数をそのまま書けば良い。  
否定は`not`

```yaml
  when:
    - not disable_foobar
```

インベントリファイル(ini形式)のbooleanっぽい変数を使う場合は`bool`に変換して使う。

```yaml
  when:
    - not (flag | bool)
```

### 条件がint型

```yml
  tasks:
    - name: len bool
      debug:
      loop:
        - 1
        - 0
        - -1
      when: item
```

`0`の場合はskip。  
`1`, `-1`は処理対象。

ansible-core 2.11.3で確認

### 条件がstring型

```yml
    - name: str
      debug:
      loop:
        - "a"
        - ""
      when: item
```

`""`の場合はskip。  
`"a"`の場合は処理される。

ansible-core 2.11.3で確認 / 2.9だと構文エラー(文字列のみの判定はできないっぽい？何かと比較するなど評価すればもちろんOK)

### リストで複数条件

`when`にはリスト形式で条件を複数指定できる。リストの場合は「すべての条件がtrueの場合」にタスクは実行。

```yaml
---
- hosts: localhost
  gather_facts: false
  vars:
    cond1: true
    cond2: false

  tasks:
    - debug:
        msg: "hello"
      when:
        - cond1
        - cond2
```

この場合スキップされる。

### orで複数条件

リスト形式はandになるため、`or`で条件を羅列して一つの条件式として記述する。

```yaml
when: cond == "foobar" or cond == "bazqux"
```

### 定義されてる場合/されてない場合

`foobar_value`が未定義の場合にタスク実行するには以下。

```yaml
when: foobar_value is not defined
```

## become

特権ユーザーへの昇格に使う場合が多いが、基本的には「別ユーザー権限でタスクを実行」するためのもの。  
`become_user`を使えば`su - <USER_NAME>`の動作になる。  
ただしパスワードを指定するディレクティブはないため、認証のためのパスワードは`ansible_become_password`変数を併用する。

```yaml
- name: exec by other user
  module:
  become: true
  become_user: other-user-name
  vars:
    ansible_become_password: other-user-password
```

## includeとimport

- [Re-using Ansible artifacts — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse.html)
- [include および import — Ansible Documentation](https://docs.ansible.com/ansible/2.9_ja/user_guide/playbooks_reuse_includes.html)
- [OSSはアルミニウムの翼で飛ぶ: Ansible: include の代わりに使う import_xxx, include_yyy とは](http://aikotobaha.blogspot.com/2017/12/ansible-include-importxxx-includeyyy.html)

とりあえずリンク

## check mode

`ansible-playbook`実行時のオプションで`--check`付与でdry runになるが、`check_mode`を使うことでタスク単位で強制的にチェックモードのon/offができる。

```yaml
  - name: command sample
    copy:
      src: /home/zaki/tmp.yml
      dest: /var/tmp
      mode: '0644'
    register: result
    check_mode: false
```

このパラメタを`true`にすると、`--check`を付けなくてもチェックモードで動作する。  
`false`にすると、`--check`で動かしてもチェックモードでなく実処理が行われる。

`--check`については[playbook](/Ansible/playbook/#-check)参照。

ちなみに、古いバージョンの`always_run`と機能的には同じ(値は逆)  

> Note  
> Prior to version 2.2 only the equivalent of `check_mode: no` existed. The notation for that was `always_run: yes`.
>
> <https://docs.ansible.com/ansible/2.9/user_guide/playbooks_checkmode.html#enabling-or-disabling-check-mode-for-tasks>

## changed_when

条件がtrueの場合はタスクの結果に関係なく`changed`判定にする。逆にfalseの場合は`ok`判定にする。  
タスクの結果を`register`で保持し、その内容を条件式に組み込むのもOK

```yaml
    - name: command always run and no change
      ansible.builtin.shell:
        cmd: hogehoge
      changed_when: exec_result.stdout != ''
      register: exec_result
```

`hogehoge`の実行結果で標準出力が何も無い場合は`changed`になる。

## until

[[Ansible] untilを使って非同期処理が完了するまで次のtaskを待つ - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/04/08/075732)

使用例

```yaml
    - name: install flannel
      shell: kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml

      # todo: applyの処理がunchangedの場合も、ansibleではchanged扱いになっている

    - name: wait for Node-Ready
      shell: kubectl get node --no-headers | awk '{print $2}'
      register: node_state
      until: node_state.stdout == "Ready"
      retries: 10
      delay: 5
      changed_when: false
      # 待つだけなので強制no changed
```

[initialize-kubeadm-ansible/deploy_cni.yaml at e48d1a86ef9f4d94853c18246708490f842b8058 · zaki-lknr/initialize-kubeadm-ansible](https://github.com/zaki-lknr/initialize-kubeadm-ansible/blob/e48d1a86ef9f4d94853c18246708490f842b8058/playbooks/deploy_cni.yaml#L13-L15)

include_tasksとは併用不可？

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">手元の2.10.5ですっごく雑に確認した限りだと、import_tasksは<br>when: 有効<br>loop: 「loopしたければinclude使え」とエラー<br>register: 無視される<br>until: 無視される<br>って感じでした。<br><br>include_tasksにだとloopとregisterは使えましたが、untilはnot valid attribute errorでやっぱりダメっぽい… <a href="https://t.co/9CNihs7tvh">pic.twitter.com/9CNihs7tvh</a></p>&mdash; z a k i 🌈 (@zaki_hmkc) <a href="https://twitter.com/zaki_hmkc/status/1362424065554751490?ref_src=twsrc%5Etfw">February 18, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## async + poll

[[Ansible] asyncとpollを使った非同期処理とループの並列実行 - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/04/06/214645)

## delegate_to

- [[Ansible] 処理の中でターゲットノードがコロコロ替わるときはdelegate_toを使ってみる - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/05/24/122926)
- [Controlling where tasks run: delegation and local actions — Ansible Community Documentation](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_delegation.html)
