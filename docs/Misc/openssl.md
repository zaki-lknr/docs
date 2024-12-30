# openssl

## 秘密鍵作成

### RSA秘密鍵

```console
openssl genrsa > private.key
```

鍵長を指定するには引数に指定

```console
$ openssl genrsa 4096 > private.key
$ openssl rsa -text -noout -in private.key 
Private-Key: (4096 bit, 2 primes)
:
:
```

## 証明書作成

### プライベートCA作成と署名

※ ~~OpenSSL 3 では `openssl genrsa`はdeprecatedになっている~~  
なってなかった  
[genrsa is not really deprecated although documented so · Issue #21055 · openssl/openssl](https://github.com/openssl/openssl/issues/21055)

`genpkey`使う場合は以下。

```console
# key作成
openssl genpkey -algorithm rsa -out ca.key
# crt作成
openssl req -new -x509 -days 3650 -key ca.key -subj "/CN=example.org/" -out ca.crt
```

csrを作らずに一気にcrtファイル作成は↑の通り。

csrも作るには以下(生成情報に若干の差異はあるけど)

```console
# csr作成
openssl req -new -key ca.key -subj "/CN=example.org/" -out ca.csr
# crt作成
openssl x509 -req -in ca.csr -signkey ca.key -days 3650 -out ca.crt
```

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
