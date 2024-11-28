# コレクション

## インストール

```console
ansible-galaxy collection install amazon.aws
```

`requirements.yml`使用時は以下。

```console
$ ansible-galaxy collection install -r collections/requirements.yml
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
