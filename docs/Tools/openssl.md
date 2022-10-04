# openssl

## ファイル内容確認

### RSA秘密鍵の確認

```
-----BEGIN RSA PRIVATE KEY-----
MIIJKAIBAAKCAgEAntBW2JpzhtCgXGjKsR........
:
:
.....
-----END RSA PRIVATE KEY-----
```

こんな内容の「BEGIN RSA PRIVATE KEY」で始まってENDまでのファイルは以下で確認。

```console
# openssl rsa -text -noout -in account.key 
```

### CSR(証明書署名要求)の確認

```
-----BEGIN CERTIFICATE REQUEST-----
MIIEkDCCAngCAQAwHDEaMB........
:
:
.....
-----END CERTIFICATE REQUEST-----
```

「BEGIN CERTIFICATE REQUEST」で始まってENDで終わるファイルであれば以下で確認。

```console
# openssl req -text -noout -in server.csr
```

### 証明書ファイルの確認

```
-----BEGIN CERTIFICATE-----
MIIGKTCCBRGg.......
:
:
....
-----END CERTIFICATE-----
```

「BEGIN CERTIFICATE」で始まってENDで終わっているファイルは、x509を指定して確認。

```console
# openssl x509 -text -noout -in server.crt
```
