# openssl

## 証明書作成

### オレオレ証明書作成(CommonName)

```console
openssl req -newkey rsa:4096 -nodes -sha256 -keyout /path/to/example.org.key -x509 -days 365 -out /path/to/example.org.crt -subj '/CN=example.org/'
```

### Alternative Name指定のオレオレ証明書

`appserv`と`appserv.example.org`いう名前のSANを追加する場合。

```console
openssl req -newkey rsa:4096 -nodes -sha256 -keyout server.key -x509 -days 365  -out server.crt -subj '/C=JP/' -addext "subjectAltName=DNS:appserv,DNS:appserv.example.org" 
```

### 国や都道府県等の指定

```console
openssl req ... -subj '/C=JP/ST=Tokyo/L=Daiba/CN=example.org'
```

- C(Country Name): 国(2文字)
- ST(State or Province Name): 都道府県
- L(Locality Name): 市区町村
- O(Organization Name): 組織名
- OU(Organization Unit Name): 部門名
- CN(Common Name): FQDN

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
