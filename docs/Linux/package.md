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

## apk

インストール

```
# apk add squid
```

アンインストール

```
# apk del squid
```
