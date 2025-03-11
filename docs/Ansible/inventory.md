# インベントリ

- [How to build your inventory — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
- [インベントリーの構築方法 — Ansible Documentation](https://docs.ansible.com/ansible/2.9_ja/user_guide/intro_inventory.html)

## インベントリファイルへのグループ変数定義

```ini
[servers]
host1
host2

[all:vars]
gateway=192.168.0.1

[servers:vars]
db_host=192.168.0.121
```

## localhost

- [Implicit ‘localhost’ — Ansible Documentation](https://docs.ansible.com/ansible/latest/inventory/implicit_localhost.html)
- [暗黙的な ‘localhost’ — Ansible Documentation](https://docs.ansible.com/ansible/2.9_ja/inventory/implicit_localhost.html)

ターゲットホストで`localhost`を指定した場合

```yaml
---
- hosts: localhost
  gather_facts: false

  tasks:
    - name: sample
      ping:
```

このとき、インベントリファイルに`localhost`の定義が無い場合(や、インベントリファイルを指定しない場合)は、暗黙の`localhost`として

```yaml
hosts:
  localhost:
   vars:
     ansible_connection: local
     ansible_python_interpreter: "{{ansible_playbook_python}}"
```

ってのが内部で用意され、この設定で接続される。よって、`connection: local`として動く。

```console
$ ansible-playbook localhost.yml -vvv
:
:
TASK [sample] *****************************************************
task path: /home/zaki/src/ansible-sample/inventory/localhost.yml:6
<127.0.0.1> ESTABLISH LOCAL CONNECTION FOR USER: zaki
:
:
```

こんな感じ。

インベントリに`localhost`の設定があればそれが使われる。当然`connection: local`が無ければ、設定されたコネクションプラグインが使用される(未指定ならデフォルトの`ssh`)

```ini
[localhost]
localhost
```

こんな`localhost.ini`というインベントリファイルを指定すると、

```console
$ ansible-playbook localhost.yml -i localhost.ini -vvv
:
:
TASK [sample] *****************************************************
task path: /home/zaki/src/ansible-sample/inventory/localhost.yml:6
<localhost> ESTABLISH SSH CONNECTION FOR USER: None
:
:
```

このようにssh接続となる。

## ダイナミックインベントリ

### EC2

[[Ansible / AWS] ダイナミックインベントリを使ってEC2接続先を動的に取得してAnsible実行お試し - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/06/21/091252)

```yaml
---
plugin: amazon.aws.aws_ec2
regions:
  - ap-northeast-1
```

### netbox

[[NetBox / Ansible] ダイナミックインベントリを使ってNetBoxに登録されたホスト情報をターゲットノードにAnsibleを実行 - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2021/01/29/002540)

```yaml
plugin: netbox.netbox.nb_inventory
api_endpoint: http://192.168.0.19:28080
token: 0123456789abcdef0123456789abcdef01234567
```
