# プレイブック

```yaml
- hosts: localhost
  gather_facts: false

  tasks:
  - name: hello
    debug:
      msg: hello world
```

# インベントリ

```ini
[server]
192.168.0.1
```
