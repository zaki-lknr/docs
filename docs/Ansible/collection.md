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
