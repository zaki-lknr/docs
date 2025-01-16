# Dockerインストール

## Docker

[Install Docker Engine | Docker Documentation](https://docs.docker.com/engine/install/)

各ディストリビューション毎のページ参照。

Windowsもhome含めてOK  
[Windows 10 HomeへのDocker Desktop (ver 3.0.0) インストールが何事もなく簡単にできるようになっていた (2020.12時点) - Qiita](https://qiita.com/zaki-lknr/items/db99909ba1eb27803456)

## Docker Compose

- v1: [Install Docker Compose | Docker Documentation](https://docs.docker.com/compose/install/)
- v2: [Compose V2 | Docker Documentation](https://docs.docker.com/compose/cli-command/)

## dockerグループへユーザーを追加するアレ(コピペ用)

```
$ sudo usermod -aG docker $USER
```

## Oracle Linux

### 標準

OCIでOL7.9の場合、デフォルトのリポジトリだとこれがヒットする。

```
[opc@instance-20210801-1151 ~]$ yum info docker-engine.aarch64
読み込んだプラグイン:langpacks, ulninfo
利用可能なパッケージ
名前                : docker-engine
アーキテクチャー    : aarch64
バージョン          : 19.03.11.ol
リリース            : 13.el7
容量                : 14 M
リポジトリー        : ol7_oci_included/aarch64
要約                : The open-source application container engine
URL                 : https://dockerproject.org
ライセンス          : ASL 2.0
説明                : Docker is an open source project to build, ship and run any application as a
                    : lightweight container.
                    : 
                    : Docker containers are both hardware-agnostic and platform-agnostic. This means
                    : they can run anywhere, from your laptop to the largest EC2 compute instance and
                    : everything in between - and they don't require you to use a particular
                    : language, framework or packaging system. That makes them great building blocks
                    : for deploying and scaling web apps, databases, and backend services without
                    : depending on a particular stack or provider.
```

### centos版

互換性が高そうなCentOS版の手順・リポジトリでもインストール・お試しにnginxコンテナデプロイはできた。

[Install Docker Engine on CentOS | Docker Documentation](https://docs.docker.com/engine/install/centos/)

ただし依存パッケージ(`slirp4netns`, `fuse-overlayfs`)のインストール用に、`ol7_developer`リポジトリを有効にしておく必要あり。(デフォルト無効)

```
$ sudo yum-config-manager --enable ol7_developer
```

[Oracle Linux 7 (aarch64) Development | Oracle, Software. Hardware. Complete.](https://yum.oracle.com/repo/OracleLinux/OL7/developer/aarch64/index.html)

## 古いDocker

検証用に古いバージョンを入れたい場合

### Docker

ディストリビューション毎のパッケージファイルがダウンロードサイトにある。  
各ディストリビューションのバージョン毎に、その時期のDockerバージョンがアーカイブされている。  
時間差があるような(新しいFedoraに古いDockerを入れる、みたいな)場合は、プラットフォームバージョン側のバージョンを落として探してみる。

- [Ubuntu 20.04 LTS: Docker 19.03.10 - 20.10.24](https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/amd64/)
- [Fedora 36: Docker 20.10.15 - 20.10.24](https://download.docker.com/linux/fedora/36/x86_64/stable/Packages/)

### Docker Compose

例えば1.29.2であれば以下から直接バイナリをダウンロードし、`/usr/local/bin/docker-compose`あたりに配置し実行権限を付与する。

<https://github.com/docker/compose/releases/tag/1.29.2>

エラーが出た場合

```console
$ docker-compose version
[21740] Error loading Python lib '/tmp/_MEIy05DAM/libpython3.7m.so.1.0': dlopen: libcrypt.so.1: cannot open shared object file: No such file or directory
```

Fedoraの場合

```console
$ sudo dnf search libxcrypt
```

これで動く

```console
[zaki@fedora40-dev docker-20]$ docker-compose version
docker-compose version 1.29.2, build 5becea4c
docker-py version: 5.0.0
CPython version: 3.7.10
OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019
```
