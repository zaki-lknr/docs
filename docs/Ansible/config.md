# 設定

## Pythonインタプリタパス

| 設定場所        | 設定キー                                 |
| ----------- | ------------------------------------ |
| ansible.cfg | interpreter_python ([defaults]section) |
| 環境変数        | ANSIBLE_PYTHON_INTERPRETER           |
| 変数          | ansible_python_interpreter           |

## コールバックプラグイン

| 設定場所        | 設定キー                                |
| ----------- | ----------------------------------- |
| ansible.cfg | stdout_callback ([defaults]section) |
| 環境変数        | ANSIBLE_STDOUT_CALLBACK             |

## コレクションパス(2.10～)

| 設定場所        | 設定キー                                 |
| ----------- | ------------------------------------ |
| ansible.cfg | collections_path ([defaults]section) |
| 環境変数        | ANSIBLE_COLLECTIONS_PATH             |

## コレクションパス(旧)

| 設定場所        | 設定キー                                  |
| ----------- | ------------------------------------- |
| ansible.cfg | collections_paths ([defaults]section) |
| 環境変数        | ANSIBLE_COLLECTIONS_PATHS             |

## 実行後にAnsiballZファイルを消さない

| 設定場所 | 設定キー                      |
| ---- | ------------------------- |
| 環境変数 | ANSIBLE_KEEP_REMOTE_FILES |

`ANSIBLE_KEEP_REMOTE_FILES=true`をセットして実行する

```console
$ ANSIBLE_KEEP_REMOTE_FILES=true ansible-playbook -i inventory.ini playbook.yml -v ...
```

これでターゲットノード上のスクリプトファイルが残る。 基本的に消えないので手動で消す必要あり。  
ターゲットノードのどこに転送されるかは、-vvvくらいまでverboseレベルを上げておけば実行時に表示される。
