# 証明書

## 証明書をシステムワイドに設定する

### RHEL系

`/etc/pki/ca-trust/source/anchors/`以下にPEM形式の証明書ファイルを配置して`update-ca-trust`を実行する。

ちなみに`/etc/pki/ca-trust/extracted/openssl/ca-bundle.trust.crt`にマージされる。

### Debian系

`/usr/local/share/ca-certificates/`以下に同じようにPEM形式の証明書ファイルを配置し`update-ca-certificates`を実行する。

```
$ sudo update-ca-certificates 
Updating certificates in /etc/ssl/certs...
1 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
```

こちらは`/etc/ssl/certs/`以下に実ファイルへのsymlinkが作成される。
