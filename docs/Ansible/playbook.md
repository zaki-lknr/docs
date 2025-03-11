# playbookサンプル

## ディレクティブ

[Playbook Keywords — Ansible Documentation](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html)

[使用例](directives.md)

## playbook

```yaml
- hosts: all
  gather_facts: false

  tasks:
  - name: hello
    debug:
      msg: hello world
```

## inventory

```ini
[server]
192.168.0.1
```
