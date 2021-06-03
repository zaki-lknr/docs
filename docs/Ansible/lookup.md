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

