# AWX

## document

- [Ansible AWX Documentation — Ansible AWX community documentation](https://ansible.readthedocs.io/projects/awx/en/latest/)
- [Ansible AWX Operator Documentation](https://ansible.readthedocs.io/projects/awx-operator/en/latest/)

## helmインストール版

基本的に情報が少ない。  
細かいパラメタはGitHubのREADMEに記載あり。

- [AWX Operator Helm Chart](https://ansible.readthedocs.io/projects/awx-operator-helm/index.html)
- [awx-operator Helm charts | awx-operator-helm](https://ansible-community.github.io/awx-operator-helm/)
- [ansible-community/awx-operator-helm: Helm chart for the AWX operator.](https://github.com/ansible-community/awx-operator-helm/tree/main)

## arm64版ビルド

arm64版はイメージが公開されてないのでビルドする必要がある。  
2022.02.15時点の内容。2.16にAWX 2.0.0がリリースされてる。

### awx-operator

0.16.1 (devel)で実施

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

19.5.1で実施。

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

awx-eeもarm版をbuild&pushして…

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
  ee_images:
    - name: awx-demo-ee
      image: zakihmkc/awx-ee
```

```console
$ kubectl apply -f awx-demo.yml -n awx
awx.awx.ansible.com/awx-demo created
```

反映されない。。
`ee_images`の設定方法わからず。。
ドキュメントよく見ると、すぐ近くに`control_plane_ee_image`について書かれている。

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
  ee_images:
    - name: awx-ee-arm64
      image: zakihmkc/awx-ee
  control_plane_ee_image: zakihmkc/awx-ee
```

これでarm用にビルドしたイメージをpullできた。  
が…

```console
$ kubectl logs -n awx awx-demo-5888cdc5cc-dtqtt -c awx-demo-ee
/usr/bin/receptor: /usr/bin/receptor: cannot execute binary file
```

中で使ってる実行バイナリがarmじゃない？

```console
$ docker run -it --rm zakihmkc/awx-ee cat /usr/bin/receptor | file -
/dev/stdin: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, Go BuildID=dzUF3PWwMDDpxbzJG4PA/ROcMoX3MTms06DLSdROU/2q0gqTdR4VkvLhOjbFwW/_sKtclM7e0bA3VOTpJ50, not stripped
```

あぁ、、なるほど…  
ビルドログを見返すと、

```console
Step 21/26 : COPY --from=quay.io/ansible/receptor:devel /usr/bin/receptor /usr/bin/receptor
devel: Pulling from ansible/receptor
```

ってのが出力されてた。(awx-ee)

### awx-ee

0.6.0で実施

```console
$ pip install tox
```

```console
$ tox -edocker
```

エラーになった。ベースイメージがこれもamd64しかないっぽい。

```console
Digest: sha256:67164fe74446b5cc40f7ff48d69c2cf5a09d234103982a870e8dd90f0cce4fcf
Status: Downloaded newer image for quay.io/ansible/ansible-runner:latest
 ---> fe0ebe0eae7f
Step 4/26 : ARG ANSIBLE_GALAXY_CLI_COLLECTION_OPTS=
 ---> [Warning] The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
 ---> Running in ddcc58ce4a3e
Removing intermediate container ddcc58ce4a3e
 ---> 29bdd8b42c78
Step 5/26 : USER root
 ---> [Warning] The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
 ---> Running in ec96ecc53899
Removing intermediate container ec96ecc53899
 ---> 047557a18fd9
Step 6/26 : ADD _build /build
 ---> 4aae6069de8c
Step 7/26 : WORKDIR /build
 ---> [Warning] The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
 ---> Running in 84bc2b6aa898
Removing intermediate container 84bc2b6aa898
 ---> 96ca9bbcffce
Step 8/26 : RUN ansible-galaxy role install -r requirements.yml --roles-path /usr/share/ansible/roles
 ---> [Warning] The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
 ---> Running in 52ab0ab8b6c9
standard_init_linux.go:228: exec user process caused: exec format error
The command '/bin/sh -c ansible-galaxy role install -r requirements.yml --roles-path /usr/share/ansible/roles' returned a non-zero code: 1

An error occured (rc=1), see output line(s) above for details.
ERROR: InvocationError for command /home/ubuntu/src/awx-ee/.tox/docker/bin/ansible-builder build -v3 -c . -t quay.io/ansible/awx-ee --container-runtime=docker (exited with code 1)
```

runnerもbuilderもarm版が必要っぽいな

runnerとbuilderを用意するとビルド成功。

```console
Removing intermediate container dcdffc16d888
 ---> 5b60adcb5b7b
Successfully built 5b60adcb5b7b
Successfully tagged quay.io/ansible/awx-ee:latest

Complete! The build context can be found at: /home/ubuntu/src/awx/awx-ee
______________________________________________________ summary _______________________________________________________
  docker: commands succeeded
  congratulations :)
```

```console
$ docker image ls quay.io/ansible/awx-ee
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
quay.io/ansible/awx-ee   latest    5b60adcb5b7b   5 minutes ago   1.58GB
```

あ、イメージ名…

```console
$ docker tag quay.io/ansible/awx-ee:latest zakihmkc/awx-ee:latest
$ docker push zakihmkc/awx-ee:latest
```

### ansible-runner

develブランチで実施

```console
$ make image
```

```console
Status: Downloaded newer image for quay.io/ansible/python-builder:latest
 ---> e86f8912b919
Step 6/21 : ARG ANSIBLE_BRANCH
 ---> [Warning] The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
 ---> Running in 4efe4c1dbf0d
Removing intermediate container 4efe4c1dbf0d
 ---> b29ee85f97c3
Step 7/21 : ARG ZUUL_SIBLINGS
 ---> [Warning] The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
 ---> Running in e3770bf07693
Removing intermediate container e3770bf07693
 ---> 3bc46246a2c3
Step 8/21 : COPY . /tmp/src
 ---> 5c8193d3adec
Step 9/21 : RUN if [ "$ANSIBLE_BRANCH" != "" ] ; then       echo "Installing requirements.txt / upper-constraints.txt for Ansible $ANSIBLE_BRANCH" ;       cp /tmp/src/tools/requirements-$ANSIBLE_BRANCH.txt /tmp/src/requirements.txt ;       cp /tmp/src/tools/upper-constraints-$ANSIBLE_BRANCH.txt /tmp/src/upper-constraints.txt ;     else       echo "Installing requirements.txt" ;       cp /tmp/src/tools/requirements.txt /tmp/src/requirements.txt ;     fi     && cp /tmp/src/tools/build-requirements.txt /tmp/src/build-requirements.txt     && cp /tmp/src/tools/bindep.txt /tmp/src/bindep.txt
 ---> [Warning] The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
 ---> Running in a3b89d0a4b54
standard_init_linux.go:228: exec user process caused: exec format error
The command '/bin/sh -c if [ "$ANSIBLE_BRANCH" != "" ] ; then       echo "Installing requirements.txt / upper-constraints.txt for Ansible $ANSIBLE_BRANCH" ;       cp /tmp/src/tools/requirements-$ANSIBLE_BRANCH.txt /tmp/src/requirements.txt ;       cp /tmp/src/tools/upper-constraints-$ANSIBLE_BRANCH.txt /tmp/src/upper-constraints.txt ;     else       echo "Installing requirements.txt" ;       cp /tmp/src/tools/requirements.txt /tmp/src/requirements.txt ;     fi     && cp /tmp/src/tools/build-requirements.txt /tmp/src/build-requirements.txt     && cp /tmp/src/tools/bindep.txt /tmp/src/bindep.txt' returned a non-zero code: 1
make: *** [Makefile:95: image] Error 1
```

これまたベースやビルドに使う `quay.io/ansible/python-base:latest` と `quay.io/ansible/python-builder:latest` がamd64向けっぽい。

後述の`python-base`と`python-builder`を↑のイメージ名でビルドして再実行すればOK

```console
Step 20/21 : ENTRYPOINT ["entrypoint"]
 ---> Running in efe6471171c6
Removing intermediate container efe6471171c6
 ---> 8b899620e92f
Step 21/21 : CMD ["ansible-runner", "run", "/runner"]
 ---> Running in 5486cf162432
Removing intermediate container 5486cf162432
 ---> 7db86ada250a
Successfully built 7db86ada250a
Successfully tagged quay.io/ansible/ansible-runner:latest
docker tag quay.io/ansible/ansible-runner quay.io/ansible/ansible-runner:devel
```

### python-base

mainブランチで実施

[ansible/python-base-image: A container image for running python projects](https://github.com/ansible/python-base-image)

ビルド用のMakefileの類はなにも無し。  
`Containerfile`あるので、これで`docker build`した。

```console
$ docker build -f Containerfile -t quay.io/ansible/python-base .
```

これはうまく行った。

```console
:
:
Step 16/17 : RUN rm -rf $REMOTE_SOURCE_DIR
 ---> Running in e45372606b30
Removing intermediate container e45372606b30
 ---> 1fc6e4db77c4
Step 17/17 : ENTRYPOINT ["/usr/local/bin/dumb-init", "--"]
 ---> Running in e711f13da032
Removing intermediate container e711f13da032
 ---> 84c14ff10f30
Successfully built 84c14ff10f30
Successfully tagged quay.io/ansible/python-base:latest
```

```console
$ docker image ls quay.io/ansible/python-base
REPOSITORY                    TAG       IMAGE ID       CREATED              SIZE
quay.io/ansible/python-base   latest    84c14ff10f30   About a minute ago   768MB
```

### python-builder

mainブランチで実施

[ansible/python-builder-image: A container image for building python projects](https://github.com/ansible/python-builder-image)

これも特にビルド用のツール無し。

```console
$ docker build -f Containerfile -t quay.io/ansible/python-builder:latest .
```

これもうまくいった。

```console
:
:
Step 18/19 : WORKDIR /
 ---> Running in 561b64ed5ff6
Removing intermediate container 561b64ed5ff6
 ---> 756c2f3aab76
Step 19/19 : RUN rm -rf $REMOTE_SOURCE_APP_DIR
 ---> Running in 0315f4ab8cbc
Removing intermediate container 0315f4ab8cbc
 ---> c832710ee7dd
Successfully built c832710ee7dd
Successfully tagged quay.io/ansible/python-builder:latest
```

```console
$ docker image ls quay.io/ansible/python-builder
REPOSITORY                       TAG       IMAGE ID       CREATED          SIZE
quay.io/ansible/python-builder   latest    c832710ee7dd   51 seconds ago   854MB
```

```console
$ docker image ls quay.io/ansible/ansible-runner
REPOSITORY                       TAG       IMAGE ID       CREATED              SIZE
quay.io/ansible/ansible-runner   devel     7db86ada250a   About a minute ago   962MB
quay.io/ansible/ansible-runner   latest    7db86ada250a   About a minute ago   962MB
```

### ansible-builder

release_1.0で実施

[ansible/ansible-builder: An Ansible execution environment builder](https://github.com/ansible/ansible-builder)

これもrunnerと同じく、`Makefile`に`image`ターゲットがあるのでこれを実行。
ビルド中のログを見るとpython-baseとpython-builderに依存している。

```console
$ make image
```

```console
$ docker image ls quay.io/ansible/ansible-builder
REPOSITORY                        TAG                   IMAGE ID       CREATED          SIZE
quay.io/ansible/ansible-builder   1.0.0a2-99-g38a0104   e0d5adabb6c3   46 seconds ago   858MB
quay.io/ansible/ansible-builder   latest                e0d5adabb6c3   46 seconds ago   858MB
```

### receptor

1.0.0a2ブランチで実施

[ansible/receptor: Project Receptor is a flexible multi-service relayer with remote execution and orchestration capabilities linking controllers with executors across a mesh of nodes.](https://github.com/ansible/receptor)

デフォルトは`podman`を使うみたいなので`docker`を指定して実行。

```console
$ make container CONTAINERCMD=docker
```

エラーになった。

```console
Exception: Versioning for this project requires either an sdist tarball, or access to an upstream git repository. It's also possible that there is a mismatch between the package name in setup.cfg and the argument given to pbr.version.VersionInfo. Project name receptorctl was given, but was not able to be found.
error in setup command: Error parsing /source/receptorctl/setup.cfg: Exception: Versioning for this project requires either an sdist tarball, or access to an upstream git repository. It's also possible that there is a mismatch between the package name in setup.cfg and the argument given to pbr.version.VersionInfo. Project name receptorctl was given, but was not able to be found.
make: git: Command not found
```

んー、develのままだったのが悪かったかな。

```console
$ git switch release_1.1 
Switched to branch 'release_1.1'
Your branch is up to date with 'origin/release_1.1'.
```

でも、別のエラーが出る。。よくわからん。  
ディレクトリ掘ったら`packaging/container/Dockerfile`があった…が、このファイルは`make`でビルドするときに使われるのと一緒だ。。

ビルドのログをよくみると

```console
Step 3/25 : RUN curl -LO https://go.dev/dl/go1.16.13.linux-amd64.tar.gz
 ---> Using cache
 ---> b1bf06de576f
Step 4/25 : RUN tar -C /usr/local -xzf go1.16.13.linux-amd64.tar.gz
 ---> Using cache
 ---> 7b134322bd9b
```

みたいなのあった。これか。  
以下変更

```diff
$ git diff
diff --git a/packaging/container/Dockerfile b/packaging/container/Dockerfile
index ead007a..1fc7416 100644
--- a/packaging/container/Dockerfile
+++ b/packaging/container/Dockerfile
@@ -2,9 +2,9 @@ FROM quay.io/centos/centos:stream8 as builder
 
 RUN dnf -y update && dnf install -y make python3 python3-pip python3-wheel
 
-RUN curl -LO https://go.dev/dl/go1.16.13.linux-amd64.tar.gz
+RUN curl -LO https://go.dev/dl/go1.16.13.linux-arm64.tar.gz
 
-RUN tar -C /usr/local -xzf go1.16.13.linux-amd64.tar.gz
+RUN tar -C /usr/local -xzf go1.16.13.linux-arm64.tar.gz
 
 ADD source.tar.gz /source
 WORKDIR /source
```

これでビルド。同じエラーはでたが処理は続行した。

```console
:
:
Step 24/25 : ENTRYPOINT ["/usr/bin/tini", "--"]
 ---> Running in c19287787c6c
Removing intermediate container c19287787c6c
 ---> d90dae43fbbf
Step 25/25 : CMD ["/usr/bin/receptor", "-c", "/etc/receptor/receptor.conf"]
 ---> Running in e8debaee8c0c
Removing intermediate container e8debaee8c0c
 ---> 376efbc54f61
Successfully built 376efbc54f61
Successfully tagged receptor:latest
```

あ、イメージ名…

```console
$ docker image ls receptor
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
receptor     latest    376efbc54f61   51 seconds ago   777MB
```

とりあえずセット

```console
$ docker tag receptor:latest quay.io/ansible/receptor:devel
```

## AWXでジョブが実行できない件(調査中)

AWXで以下のエラーが出てるやつ。

```console
Traceback (most recent call last):
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/awx/main/tasks/jobs.py", line 449, in run
    self.pre_run_hook(self.instance, private_data_dir)
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/awx/main/tasks/jobs.py", line 979, in pre_run_hook
    RunProjectUpdate.make_local_copy(job.project, private_data_dir, scm_revision=job_revision)
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/awx/main/tasks/jobs.py", line 1380, in make_local_copy
    source_branch = git_repo.create_head(tmp_branch_name, p.scm_revision)
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/git/repo/base.py", line 386, in create_head
    return Head.create(self, path, commit, force, logmsg)
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/git/refs/symbolic.py", line 543, in create
    return cls._create(repo, path, cls._resolve_ref_on_create, reference, force, logmsg)
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/git/refs/symbolic.py", line 510, in _create
    ref.set_reference(target, logmsg)
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/git/refs/symbolic.py", line 326, in set_reference
    assure_directory_exists(fpath, is_file=True)
  File "/var/lib/awx/venv/awx/lib64/python3.9/site-packages/git/util.py", line 177, in assure_directory_exists
    os.makedirs(path, exist_ok=True)
  File "/usr/lib64/python3.9/os.py", line 225, in makedirs
    mkdir(name, mode)
PermissionError: [Errno 13] Permission denied: '/var/lib/awx/projects/_6__demo_project/.git/refs/heads/awx_internal'
```

オリジナルのamd64版、Dockerイメージのユーザー権限

```console
[zaki@cloud-dev2 ~]$ docker run -it --rm quay.io/ansible/awx-ee:0.6.0 bash
bash-4.4# id
uid=0(root) gid=0(root) groups=0(root)
bash-4.4# exit
[zaki@cloud-dev2 ~]$ docker run -it --rm quay.io/ansible/awx:20.0.0 bash
bash-5.1$ id
uid=1000 gid=0(root) groups=0(root)
bash-5.1$ 
```

arm64でビルドしたDockerイメージのユーザー権限

```console
ubuntu@oci-ap-a1-ubuntu02:~$ docker run -it --rm zakihmkc/awx-ee:0.6.0 bash
bash-4.4# id
uid=0(root) gid=0(root) groups=0(root)
bash-4.4# exit
exit
ubuntu@oci-ap-a1-ubuntu02:~$ docker run -it --rm zakihmkc/awx:20.0.0 bash
bash-5.1$ id
uid=1000 gid=0(root) groups=0(root)
bash-5.1$ exit
exit
```

Docker上だと同一。

じゃあk8s上だと…
amd64版はこれ。

```console
[zaki@cloud-dev2 ~]$ kubectl  exec -it -n awx awx-demo-74d8979549-bszp6 -c awx-demo-ee -- bash 
bash-4.4$ id
uid=1000(runner) gid=0(root) groups=0(root)
bash-4.4$ exit
[zaki@cloud-dev2 ~]$ kubectl  exec -it -n awx awx-demo-74d8979549-bszp6 -c awx-demo-task -- bash
bash-5.1$ id
uid=1000(awx) gid=0(root) groups=0(root)
bash-5.1$ exit
exit
```

arm64版。

```console
ubuntu@oci-ap-a1-ubuntu02:~$ kubectl exec -n awx -it awx-demo-7847cd4bb-wd4w4 -c awx-demo-ee -- bash
bash-4.4# id
uid=0(root) gid=0(root) groups=0(root)
bash-4.4# exit
ubuntu@oci-ap-a1-ubuntu02:~$ kubectl exec -n awx -it awx-demo-7847cd4bb-wd4w4 -c awx-demo-task -- bash
bash-5.1$ id
uid=1000(awx) gid=0(root) groups=0(root)
bash-5.1$ 
exit
```

eeの実行権限がここだけ`root`になっている。

実行権限じゃなくて`/etc/passwd`かも。

amd64のオリジナル版

```console
[zaki@cloud-dev2 ~]$ kubectl  exec -it -n awx awx-demo-74d8979549-bszp6 -c awx-demo-ee -- bash
bash-4.4$ id
uid=1000(runner) gid=0(root) groups=0(root)
bash-4.4$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
runner:x:1000:0:,,,:/home/runner:/bin/bash
```

arm64向けにビルドした版

```console
ubuntu@oci-ap-a1-ubuntu02:~/tmp$ kubectl exec -n awx -it awx-demo-84b4b75d85-9txt2 -c awx-demo-ee -- bash
bash-4.4# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
runner:x:0:0:,,,:/home/runner:/bin/bash
bash-4.4# exit
```

そもそも`runner`ユーザーのUIDがおかしい感じ。

ベースイメージの`ansible-runner`は？

```console
[zaki@cloud-dev2 ~]$ docker run -it --rm quay.io/ansible/ansible-runner:latest bash
bash-4.4# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
runner:x:0:0:,,,:/home/runner:/bin/bash
bash-4.4# 
```

```console
ubuntu@oci-g-a1-ubuntu:~$ docker run -it --rm quay.io/ansible/ansible-runner:latest bash
bash-4.4# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
runner:x:0:0:,,,:/home/runner:/bin/bash
bash-4.4# exit
```

この時点では同じ模様。
てことはEEのビルド時に`/etc/passwd`を変更する何かが抜けてるのかな
