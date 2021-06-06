# パッケージ操作

## インストール済みのパッケージ一覧出力

```console
$ dpkg -l      # debian
$ rpm -qa      # redhat
```

[パッケージ管理あれこれ / [cygwin][debian][redhat][Solaris][command] | 戯術者の日記](http://www.jp-z.jp/changelog/2005-11-05-1.html)

## インストール済みのパッケージ foo に含まれるファイル一覧

```
$ dpkg -L foo     # debian
$ rpm -ql foo     # redhat
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

