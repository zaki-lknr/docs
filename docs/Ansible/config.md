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
