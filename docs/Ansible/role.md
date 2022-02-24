# Role

## ロールの雛形を作る

```console
$ ansible-galaxy role init sample_role
```

こんな感じ

```console
$ find 
.
./sample_role
./sample_role/.travis.yml
./sample_role/README.md
./sample_role/defaults
./sample_role/defaults/main.yml
./sample_role/files
./sample_role/handlers
./sample_role/handlers/main.yml
./sample_role/meta
./sample_role/meta/main.yml
./sample_role/tasks
./sample_role/tasks/main.yml
./sample_role/templates
./sample_role/tests
./sample_role/tests/inventory
./sample_role/tests/test.yml
./sample_role/vars
./sample_role/vars/main.yml
```

## ロールから別のロールを実行する

特に何事もなく可能。

- role2 (呼ばれる側)

```yaml
---
# roles/test2/tasks/main.yml
- name: ...省略
```

- role3 (呼ぶ側)

```yaml
---
# roles/test3/tasks/main.yml
- name: this is test3
  debug:
    msg: "this is test3"

- name: exec other role
  import_role:
    name: test2
```

- playbook

```yaml
---
# playbook.yml
- hosts: localhost
  gather_facts: false

  roles:
  - test2
  - test3
```

## ディレクトリ構成

### meta

`meta/main.yml`ファイルに以下のような定義を記述しておくことで、ロール実行時に`builde`と`runner`ロールを事前に実行することができる。

```yaml
---
dependencies:
  - role: builder
  - role: runner
```

ロールAがロールXとロールY、ロールBもロールXとロールYに依存する設定にしてる場合、ロールXとロールYは1回ずつ呼ばれる。(AとBのロール実行前でそれぞれ計2回ずつ呼ばれることは無い)
