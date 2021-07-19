# RHEL

## サブスクリプション

[制限ネットワーク環境のRHEL7でproxy経由でサブスクリプション登録とDockerインストール - zaki work log](https://zaki-hmkc.hatenablog.com/entry/2020/09/05/160946)

これで登録とサブスクリプション割り当てをまとめてできる。

```console
# subscription-manager register --auto-attach
```

## package

### RHEL8にEPELリポジトリを追加

- [EPEL8がリリースされました。使い始めましょう。 - 赤帽エンジニアブログ](https://rheb.hatenablog.com/entry/2019/08/16/EPEL8%E3%81%8C%E3%83%AA%E3%83%AA%E3%83%BC%E3%82%B9%E3%81%95%E3%82%8C%E3%81%BE%E3%81%97%E3%81%9F%E3%80%82%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%81%BE%E3%81%97%E3%82%87%E3%81%86%E3%80%82)
- [EPEL - Fedora Project Wiki](https://fedoraproject.org/wiki/EPEL#Quickstart)

RHEL8用のコマンドを実行してリポジトリを追加する

```console
[zaki@rhel8-node ~]$ sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
Updating Subscription Management repositories.
Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)                    13 kB/s | 4.1 kB     00:00    
Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)                 19 kB/s | 4.5 kB     00:00    
Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)                6.5 MB/s |  31 MB     00:04    
epel-release-latest-8.noarch.rpm                                         30 kB/s |  23 kB     00:00    
依存関係が解決しました。
========================================================================================================
 パッケージ                アーキテクチャー    バージョン               リポジトリー              サイズ
========================================================================================================
インストール:
 epel-release              noarch              8-11.el8                 @commandline               23 k

トランザクションの概要
========================================================================================================
インストール  1 パッケージ

合計サイズ: 23 k
インストール後のサイズ: 35 k
これでよろしいですか? [y/N]: y
パッケージのダウンロード:
トランザクションの確認を実行中
トランザクションの確認に成功しました。
トランザクションのテストを実行中
トランザクションのテストに成功しました。
トランザクションを実行中
  準備             :                                                                                1/1 
  インストール中   : epel-release-8-11.el8.noarch                                                   1/1 
  scriptletの実行中: epel-release-8-11.el8.noarch                                                   1/1 
  検証             : epel-release-8-11.el8.noarch                                                   1/1 
Installed products updated.

インストール済み:
  epel-release-8-11.el8.noarch                                                                          

完了しました!
[zaki@rhel8-node ~]$ 
```

```console
[zaki@rhel8-node ~]$ ls -l /etc/yum.repos.d/
合計 168
-rw-r--r--. 1 root root   1480  6月  8 09:29 epel-modular.repo
-rw-r--r--. 1 root root   1562  6月  8 09:29 epel-playground.repo
-rw-r--r--. 1 root root   1579  6月  8 09:29 epel-testing-modular.repo
-rw-r--r--. 1 root root   1516  6月  8 09:29 epel-testing.repo
-rw-r--r--. 1 root root   1417  6月  8 09:29 epel.repo
-rw-r--r--. 1 root root 149513  7月  2 19:58 redhat.repo
```

```console
[zaki@rhel8-node ~]$ sudo dnf search sshpass
Updating Subscription Management repositories.
Extra Packages for Enterprise Linux Modular 8 - x86_64                  652 kB/s | 798 kB     00:01    
Extra Packages for Enterprise Linux 8 - x86_64                          7.2 MB/s |  10 MB     00:01    
======================================== 名前 完全一致: sshpass ========================================
sshpass.x86_64 : Non-interactive SSH authentication utility
```
