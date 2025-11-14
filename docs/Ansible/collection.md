# コレクション

- [Installing collections — Ansible Community Documentation](https://docs.ansible.com/projects/ansible/latest/collections_guide/collections_installing.html)

## インストール

```console
ansible-galaxy collection install amazon.aws
```

バージョン指定

```console
ansible-galaxy collection install ansible.netcommon:2.1.0
```

複数

```console
ansible-galaxy collection install ansible.netcommon:2.1.0 ansible.utils:2.2.0 seiko.smartcs:1.3.0
```

`requirements.yml`使用時は以下。

```console
$ ansible-galaxy collection install -r collections/requirements.yml
```

### requirements.yml書式

コレクション名のみ(バージョンは最新)

```yaml
---
collections:
- community.general
```

バージョン指定

```yaml
---
collections:
- name: ansible.netcommon
  version: 2.1.0
- name: ansible.utils
  version: 2.2.0
- name: seiko.smartcs
  version: 1.3.0
```

Gitリポジトリ

```yaml
---
collections:
  - name: https://github.com/zaki-lknr/esxissh-ansible.git
    version: main
    type: git
```

## リダイレクト設定

<https://github.com/ansible/ansible/blob/devel/lib/ansible/config/ansible_builtin_runtime.yml>

このymlファイルの定義に従い、FQCN表記でないものはリダイレクトされる。  
moduleに限らずactionプラグインやconnectionプラグインの定義もある。

## 依存コレクションの確認

`galaxy.yml`の`dependencies`を確認する。  
例えばcisco.iosのv7.0.0であれば以下の通り。

```yaml
dependencies:
  "ansible.netcommon": ">=6.0.0"
```

<https://github.com/ansible-collections/cisco.ios/blob/v7.0.0/galaxy.yml#L4-L5>
