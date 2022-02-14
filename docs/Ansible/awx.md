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

### awx

Makefileあるけど使い方がよくわからなかった。
tools/ansible/build.ymlがあるので、こっちでビルドできた。

```console
$ cd tools/ansible/
$ ansible-playbook build.yml
```

```console
$ docker images ansible/awx
REPOSITORY    TAG             IMAGE ID       CREATED         SIZE
ansible/awx   19.5.2.dev117   5c0f58543bbc   5 minutes ago   696MB
```

ビルドできたらpush

```console
$ docker tag ansible/awx:19.5.2.dev117 zakihmkc/awx:19.5.2.dev117-arm
$ docker push zakihmkc/awx:19.5.2.dev117-arm
```

この状態でデプロイすると、

```console
$ cat awx-demo.yml 
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
spec:
  service_type: nodeport
  image: zakihmkc/awx
  image_version: 19.5.2.dev117-arm
$ kubectl apply -f awx-demo.yml -n awx
awx.awx.ansible.com/awx-demo created
$ kubectl get pod -n awx 
NAME                                             READY   STATUS    RESTARTS      AGE
awx-operator-controller-manager-59584d6d-cfvcw   2/2     Running   0             60m
awx-demo-postgres-0                              1/1     Running   0             83s
awx-demo-64b6ddfbb5-td57c                        3/4     Error     3 (30s ago)   73s
```

あー、、

```console
(awx-build) ubuntu@oci-g-a1-ubuntu:~/src/awx-operator$ kubectl logs -n awx awx-demo-64b6ddfbb5-td57c -c awx-demo-ee
standard_init_linux.go:228: exec user process caused: exec format error
```

このイメージもarm版必要だった。
