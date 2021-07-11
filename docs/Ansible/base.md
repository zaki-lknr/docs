# 基本・ネタ帳

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