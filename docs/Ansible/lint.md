# Ansible Lint

## install

```
$ pip install ansible-lint==4.3.7
```

## sample

```yaml
---
- hosts: localhost
  gather_facts: false
  vars:
    value: hello
  
  tasks:
  - name: print result
    debug:
      msg: "{{ value}}"
```

`"{{ value }}"`の参照と、あと`tasks`の前の行は空行でなく2文字スペースが入っている状態。


```console
$ ansible-lint sample.yml 
WARNING  Listing 2 violation(s) that are fatal
[201] Trailing whitespace
sample.yml:6
  

[206] Variables should have spaces before and after: {{ var_name }}
sample.yml:10
      msg: "{{ value}}"

You can skip specific rules or tags by adding them to your configuration file:       

┌───────────────────────────────────────────────────────────────────────────────────┐
│ # .ansible-lint                                                                   │
│ warn_list:  # or 'skip_list' to silence them completely                           │
│   - '201'  # Trailing whitespace                                                  │
│   - '206'  # Variables should have spaces before and after: {{ var_name }}        │
└───────────────────────────────────────────────────────────────────────────────────┘
```

もしくは `-p` を付けて

```console
$ ansible-lint sample.yml -p
WARNING  Listing 2 violation(s) that are fatal
sample.yml:6: [E201] Trailing whitespace
sample.yml:10: [E206] Variables should have spaces before and after: {{ var_name }}
You can skip specific rules or tags by adding them to your configuration file:       

┌───────────────────────────────────────────────────────────────────────────────────┐
│ # .ansible-lint                                                                   │
│ warn_list:  # or 'skip_list' to silence them completely                           │
│   - '201'  # Trailing whitespace                                                  │
│   - '206'  # Variables should have spaces before and after: {{ var_name }}        │
└───────────────────────────────────────────────────────────────────────────────────┘
```
