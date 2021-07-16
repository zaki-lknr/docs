# ディレクティブ

[Playbook Keywords — Ansible Documentation](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html)

## ループ

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
`false`にすると、`--check`がなくてもチェックモードで動作する。

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
