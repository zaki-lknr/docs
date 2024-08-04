# Node.js

## インストール

Fedora40

```console
dnf info nodejs
:
:
名前         : nodejs
エポック     : 1
バージョン   : 20.12.2
リリース     : 1.fc40
Arch         : x86_64
:
```

`dnf`でインストールする。

```console
$ sudo dnf install nodejs
依存関係が解決しました。
===============================================================================================================================
 パッケージ                           アーキテクチャー    バージョン                                リポジトリー         サイズ
===============================================================================================================================
インストール:
 nodejs                               x86_64              1:20.12.2-1.fc40                          updates               51 k
依存関係のインストール:
 nodejs-cjs-module-lexer              noarch              1.2.3-5.fc40                              fedora                37 k
 nodejs-libs                          x86_64              1:20.12.2-1.fc40                          updates               16 M
 nodejs-undici                        noarch              6.11.1-2.fc40                             updates              292 k
弱い依存関係のインストール:
 nodejs-docs                          noarch              1:20.12.2-1.fc40                          updates              8.2 M
 nodejs-full-i18n                     x86_64              1:20.12.2-1.fc40                          updates              8.4 M
 nodejs-npm                           x86_64              1:10.5.0-1.20.12.2.1.fc40                 updates              2.2 M

トランザクションの概要
===============================================================================================================================
インストール  7 パッケージ

ダウンロードサイズの合計: 35 M
インストール後のサイズ: 185 M
これでよろしいですか? [y/N]: 
```

## yarnのインストール

root権限が必要

```console
$ npm install -g yarn
npm ERR! code EACCES
npm ERR! syscall mkdir
npm ERR! path /usr/local/lib/node_modules
npm ERR! errno -13
npm ERR! Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules'
npm ERR!  [Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules'] {
npm ERR!   errno: -13,
npm ERR!   code: 'EACCES',
npm ERR!   syscall: 'mkdir',
npm ERR!   path: '/usr/local/lib/node_modules'
npm ERR! }
npm ERR! 
npm ERR! The operation was rejected by your operating system.
npm ERR! It is likely you do not have the permissions to access this file as the current user
npm ERR! 
npm ERR! If you believe this might be a permissions issue, please double-check the
npm ERR! permissions of the file and its containing directories, or try running
npm ERR! the command again as root/Administrator.

npm ERR! A complete log of this run can be found in: /home/zaki/.npm/_logs/2024-08-04T07_02_22_789Z-debug-0.log
```

```console
$ sudo npm install -g yarn

added 1 package in 720ms
npm notice 
npm notice New minor version of npm available! 10.5.0 -> 10.8.2
npm notice Changelog: https://github.com/npm/cli/releases/tag/v10.8.2
npm notice Run npm install -g npm@10.8.2 to update!
npm notice 
$ which yarn
/usr/local/bin/yarn
```
