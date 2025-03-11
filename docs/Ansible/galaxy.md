# Galaxy

## document

- [Galaxy User Guide — Ansible Community Documentation](https://docs.ansible.com/ansible/latest/galaxy/user_guide.html)

## requirements.yml

`requirements.yml`の中身は以下のような感じ。

```yaml
---
collections:
  - name: zaki_lknr.esxissh
```

とか

```yaml
---
collections:
  - name: https://github.com/zaki-lknr/esxissh-ansible.git
    version: main
    type: git
```
