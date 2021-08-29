# インストール

[Install Docker Engine | Docker Documentation](https://docs.docker.com/engine/install/)

各ディストリビューション毎のページ参照。

Windowsもhome含めてOK  
[Windows 10 HomeへのDocker Desktop (ver 3.0.0) インストールが何事もなく簡単にできるようになっていた (2020.12時点) - Qiita](https://qiita.com/zaki-lknr/items/db99909ba1eb27803456)

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
