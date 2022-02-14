# AWX

## ビルド

### awx-operator

```console
$ make docker-build IMAGE_TAG_BASE=zakihmkc/awx-operator VERSION=0.16.1-arm
```

これでビルドされるイメージ名は以下の通り。

```console
:
:
Step 5/8 : RUN ansible-galaxy collection install -r ${HOME}/requirements.yml  && chmod -R ug+rwx ${HOME}/.ansible
 ---> Running in bb9cc8cc61ee
Process install dependency map
Starting collection install process
Installing 'kubernetes.core:1.2.1' to '/opt/ansible/.ansible/collections/ansible_collections/kubernetes/core'
Installing 'operator_sdk.util:0.2.0' to '/opt/ansible/.ansible/collections/ansible_collections/operator_sdk/util'
Removing intermediate container bb9cc8cc61ee
 ---> 7346f9bc9f4e
Step 6/8 : COPY watches.yaml ${HOME}/watches.yaml
 ---> fdba769a7d2f
Step 7/8 : COPY roles/ ${HOME}/roles/
 ---> 5326e818c7ed
Step 8/8 : COPY playbooks/ ${HOME}/playbooks/
 ---> 28538a5f08b7
Successfully built 28538a5f08b7
Successfully tagged zakihmkc/awx-operator:0.16.1-arm
```

```console
$ docker image ls zakihmkc/awx-operator
REPOSITORY              TAG          IMAGE ID       CREATED          SIZE
zakihmkc/awx-operator   0.16.1-arm   28538a5f08b7   45 seconds ago   516MB
```

イメージができたら`push`する。

```console
$ make docker-push IMG=zakihmkc/awx-operator:0.16.1-arm
```

ビルドしたカスタムイメージをデプロイするには

```console
$ make deploy IMG=zakihmkc/awx-operator:0.16.1-arm
```
