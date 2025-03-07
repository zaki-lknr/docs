# パッケージ操作

## インストール済みのパッケージ一覧出力

```console
$ dpkg -l      # debian
$ rpm -qa      # redhat
$ apk info     # alpine
```

[パッケージ管理あれこれ / [cygwin][debian][redhat][Solaris][command] | 戯術者の日記](http://www.jp-z.jp/changelog/2005-11-05-1.html)

## インストール済みのパッケージ foo に含まれるファイル一覧

```
$ dpkg -L foo        # debian
$ rpm -ql foo        # redhat
$ apk manifest foo   # alpine
```

## ファイル /foo/bar をインストールしたパッケージ

```console
$ dpkg -S /foo/bar    # debian
$ rpm -qf /foo/bar    # redhat
```

## 更新確認

### yum

```console
$ yum check-update 
```

## インストール可能なバージョン一覧

[yum updateするときに特定のパッケージを更新対象外にする - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/03/15/075946)

```
[root@rhel8 ~]# yum search httpd --showduplicates 
Updating Subscription Management repositories.
メタデータの期限切れの最終確認: 0:24:27 時間前の 2021年06月21日 21時36分50秒 に実施しました。
================================================ 名前 完全一致: httpd =================================================
httpd-2.4.37-39.module+el8.4.0+9658+b87b2deb.x86_64 : Apache HTTP Server
httpd-2.4.37-10.module+el8+2764+7127e69e.x86_64 : Apache HTTP Server
httpd-2.4.37-11.module+el8.0.0+2969+90015743.x86_64 : Apache HTTP Server
httpd-2.4.37-16.module+el8.1.0+4134+e6bad0ed.x86_64 : Apache HTTP Server
httpd-2.4.37-12.module+el8.0.0+4096+eb40e6da.x86_64 : Apache HTTP Server
httpd-2.4.37-21.module+el8.2.0+5008+cca404a3.x86_64 : Apache HTTP Server
httpd-2.4.37-30.module+el8.3.0+7001+0766b9e7.x86_64 : Apache HTTP Server
httpd-2.4.37-39.module+el8.4.0+9658+b87b2deb.x86_64 : Apache HTTP Server
:
:
```

## dnf

- [【 dnf 】コマンド（応用編その7）――リポジトリを追加する：Linux基本コマンドTips（375） - ＠IT](https://www.atmarkit.co.jp/ait/articles/2001/31/news006.html)

### リポジトリ一覧

有効なものの一覧。`-v`付けると詳細

```
# dnf repolist
```

無効なものの一覧。

```
# dnf repolist --disabled
```

### 指定リポジトリで提供されるパッケージ一覧

`dnf repository-packages <リポジトリ名> list`

```
# dnf repository-packages ansible-2.9-for-rhel-8-x86_64-rpms list
Updating Subscription Management repositories.
メタデータの期限切れの最終確認: 0:07:08 時間前の 2021年08月08日 12時00分34秒 に実施しました。
利用可能なパッケージ
ansible.noarch                   2.9.24-1.el8ae               ansible-2.9-for-rhel-8-x86_64-rpms
ansible-test.noarch              2.9.24-1.el8ae               ansible-2.9-for-rhel-8-x86_64-rpms
sshpass.x86_64                   1.06-3.el8ae                 ansible-2.9-for-rhel-8-x86_64-rpms
```

### 指定リポジトリを有効化/無効化

```
# dnf config-manager --enable ansible-2.9-for-rhel-8-x86_64-rpms
```

こうなる

```
[root@rhel8 ~]# dnf config-manager --enable ansible-2.9-for-rhel-8-x86_64-rpms
Updating Subscription Management repositories.
[root@rhel8 ~]# dnf repolist
Updating Subscription Management repositories.
repo id                                                            repo の名前
ansible-2.9-for-rhel-8-x86_64-rpms                                 Red Hat Ansible Engine 2.9 for RHEL 8 x86_64 (RPMs)
rhel-8-for-x86_64-appstream-rpms                                   Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
rhel-8-for-x86_64-baseos-rpms                                      Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)
```

無効化は`--disable`で。

```
# dnf config-manager --disable ansible-2.9-for-rhel-8-x86_64-rpms
```

### 強制インストール

自動化するときとか

```console
dnf install -y <package-name>
```

### rpmファイルダウンロード

```console
dnf install --downloadonly --destdir /path/to <package-name>
```

ホストにインストール済みの場合はダウンロードされないため `--installroot` を併用する。

### リポジトリ追加

```console
dnf config-manager addrepo --from-repofile=https://rpm.example.org/rpm.repo
```

以前は `dnf config-manager --add-repo https://rpm.example.org/rpm.repo` だったがオプションが変更された。

## yum

### リポジトリ一覧

有効なもの一覧

```console
# yum repolist
```

無効なもの一覧

```console
# yum repolist disabled
```

### リポジトリの有効化

```
# yum-config-manager --enable ol7_developer_EPEL
```

引数に指定するのはリポジトリIDだけど、リスト時に`/arch`が付いてる場合はそれは除く。

## apt

- option
    - [【 apt 】コマンド（基礎編）――ソフトウェアをインストールする：Linux基本コマンドTips（139） - ＠IT](https://atmarkit.itmedia.co.jp/ait/articles/1708/31/news017.html)
    - [apt - Is "upgrade --with-new-pkgs" safer than "dist-upgrade"? - Ask Ubuntu](https://askubuntu.com/questions/694403/is-upgrade-with-new-pkgs-safer-than-dist-upgrade)
- apt line関連
    - [APT の設定 (/etc/apt/sources.list) をちゃんと理解する - くじらにっき++](https://kujira16.hateblo.jp/entry/2019/10/14/190008)
    - [Ubuntuのパッケージ管理のメモ - 蒼の王座・裏口](https://sqlazure.jp/r/ubuntu/857/)

### 強制インストール

自動化するときとか

```console
apt-get install -y <package-name>
```

## apk

インストール

```
# apk add squid
```

アンインストール

```
# apk del squid
```

## リポジトリ

- [Debian -- パッケージ](https://www.debian.org/distrib/packages)
- [Ubuntu – Ubuntu パッケージ検索](https://packages.ubuntu.com/ja/)
- [Oracle Linux Yum Server](https://yum.oracle.com/)
- [Alpine Linux packages](https://pkgs.alpinelinux.org/packages)
