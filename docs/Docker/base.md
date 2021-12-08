# 基本・ネタ帳

## dockerコマンド

[docker container / image コマンド新旧比較 - Qiita](https://qiita.com/zembutsu/items/6e1ad18f0d548ce6c266)

### save

```console
$ docker save imagename:tag > imagename-tag.tar
```

または

```
$ docker save imagename:tag -o imagename-tag.tar
```

### pull

sha256ハッシュ値指定

```console
$ docker pull httpd@sha256:f2179b693cfb49baa6e7500171deea7bef755338bf165b39aedacf2b4ae28455
```
