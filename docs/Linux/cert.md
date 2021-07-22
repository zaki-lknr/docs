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

## Root CA削除

### trust

RHEL系かな？

```console
[root@cloud-dev ~]# trust list | grep local -3

pkcs11:id=%dd%14%05%0a%b1%7b%9b%09%41%2b%e6%90%a1%5d%6d%30%06%75%c7%3f;type=cert
    type: certificate
    label: local.example.org
    trust: anchor
    category: other-entry

```

```
[root@cloud-dev ~]# trust anchor --remove "pkcs11:id=%dd%14%05%0a%b1%7b%9b%09%41%2b%e6%90%a1%5d%6d%30%06%75%c7%3f;type=cert"
p11-kit: couldn't remove read-only certificate
[root@cloud-dev ~]# trust anchor --remove /var/tmp/ansible-work/ca.crt 
p11-kit: couldn't remove read-only certificate
```

これで消せるらしいんだけど、`update-ca-trust`で追加してるCAはダメなのかな。。

[第6章 共通システム証明書の使用 Red Hat Enterprise Linux 8 | Red Hat Customer Portal](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/security_hardening/using-shared-system-certificates_security-hardening)
