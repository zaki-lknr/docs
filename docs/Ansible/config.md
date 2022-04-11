# 設定

[Ansible Configuration Settings — Ansible Documentation](https://docs.ansible.com/ansible/latest/reference_appendices/config.html)

## 設定ファイル優先順位

1. `ANSIBLE_CONFIG` 環境変数
2. `ansible.cfg` (カレントディレクトリ)
3. `~/.ansible.cfg`
4. `/etc/ansible/ansible.cfg`

## 設定ファイル書式

`ansible.cfg`サンプル

```ini
[defaults]
stdout_callback = yaml
```

## 設定キー

### 接続ユーザー名

| 設定場所         | 設定名             |
| ------------ | --------------- |
| コマンドラインオプション | `-u` (`--user`) |
| 変数           | ansible_user                |

### SSH秘密鍵

| 設定場所         | 設定名                      |
| ------------ | ------------------------ |
| コマンドラインオプション | `--private-key`          |
| 環境変数         | ANSIBLE_PRIVATE_KEY_FILE |
| 変数           | ansible_private_key_file |

### Pythonインタプリタパス

| 設定場所        | 設定キー                                 |
| ----------- | ------------------------------------ |
| ansible.cfg | interpreter_python ([defaults]section) |
| 環境変数        | ANSIBLE_PYTHON_INTERPRETER           |
| 変数          | ansible_python_interpreter           |

### コールバックプラグイン

| 設定場所        | 設定キー                                |
| ----------- | ----------------------------------- |
| ansible.cfg | stdout_callback ([defaults]section) |
| 環境変数        | ANSIBLE_STDOUT_CALLBACK             |

### コレクションパス(2.10～)

| 設定場所        | 設定キー                                 |
| ----------- | ------------------------------------ |
| ansible.cfg | collections_path ([defaults]section) |
| 環境変数        | ANSIBLE_COLLECTIONS_PATH             |

### コレクションパス(旧)

| 設定場所        | 設定キー                                  |
| ----------- | ------------------------------------- |
| ansible.cfg | collections_paths ([defaults]section) |
| 環境変数        | ANSIBLE_COLLECTIONS_PATHS             |

### 実行後にAnsiballZファイルを消さない

| 設定場所 | 設定キー                      |
| ---- | ------------------------- |
| 環境変数 | ANSIBLE_KEEP_REMOTE_FILES |

`ANSIBLE_KEEP_REMOTE_FILES=true`をセットして実行する

```console
$ ANSIBLE_KEEP_REMOTE_FILES=true ansible-playbook -i inventory.ini playbook.yml -v ...
```

これでターゲットノード上のスクリプトファイルが残る。 基本的に消えないので手動で消す必要あり。  
ターゲットノードのどこに転送されるかは、-vvvくらいまでverboseレベルを上げておけば実行時に表示される。
