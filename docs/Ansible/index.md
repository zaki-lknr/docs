# 基本・ネタ帳

## コンセプト

[Ansible concepts — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/basic_concepts.html)

## リリースとメンテナンス情報

[Releases and maintenance — Ansible Documentation](https://docs.ansible.com/ansible/devel/reference_appendices/release_and_maintenance.html)

## ディレクトリ構成

[Sample Ansible setup — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/sample_setup.html)

「Sample directory layout」や「Alternative directory layout」を参照。

## Tips and trics

旧「ベストプラクティス」のページはこちら。  
[Tips and tricks — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html)

## venv作るタスク

venvにopenshiftパッケージ入れる

```yaml
    - name: install openshift
      ansible.builtin.pip:
        name: openshift
        virtualenv: /root/venv
        virtualenv_command: python3 -m venv
```

このターゲットノードのvenvを使ってAnsible実行するには以下。

[[Ansible] ターゲットノードのvenvのPythonを指定して実行する (interpreter_python / ansible_python_interpreter / PYTHONPATH) - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/05/26/230432)

この辺の設定をどこかに入れればOK

```yaml
ansible_python_interpreter: /root/venv/bin/python
```

pipは最新にしつつ、バージョン指定のパッケージも入れるならこんな感じ。  
最新だと`ansible==4.2.0`が入る場合でも、`4.1.0`がインストールされる。

```yaml
    - name: create venv
      ansible.builtin.pip:
        name:
          - pip
          - ansible==4.1.0
        state: latest
        virtualenv: ~/venv/ansible4.1.0
        virtualenv_command: python3 -m venv
```

# ドキュメント

## 索引

[Index — Ansible Documentation](https://docs.ansible.com/ansible/latest/genindex.html)

## ansible-doc

```console
$ ansible-doc <module-name>
```

```console
$ ansible-doc <module-fqcn>
```

モジュールパスを指定するには

```console
$ ansible-doc -M /path/to/collection-path/ansible_collections/namespace/collectionname/plugins/modules hogehoge
```

パスが合わない場合は、エラーメッセージに「これらのパスにそんなモジュールないんだが？」とパス一覧が表示されるので、そこから調整する。
