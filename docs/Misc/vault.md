# HashiCorp Vault

## 認証

### ログイン

```console
vault login [認証トークン]
```

### ログイン中のtoken情の報等

```console
vault token lookup
```

トークンを省略すれば対話的に聞いてくる

### OIDCでCLIログイン

```console
vault login -method=oidc [-path=***] role=****
```

## 初期処理

### init

```console
vault operator init
```

Unseal KeyとRoot Tokenが出力されるのでメモしておく

### unseal

```console
vault operator unseal
```

Unseal keyの入力を求められるので`init`実行時に出力されたキーを入力する。  
`Sealed  true`が`Sealed  false`になるまで繰り返す。

## secret engine

### secret一覧

```console
vault secrets list
```

### secret engineの有効化

データ保持用の入れ物を作るイメージ。  
KVシークレットエンジンであれば以下の通り。

```console
vault secrets enable -path=my-secret kv
```

これで`my-secret`というパスにKVシークレットエンジンが作成される。

### KV secret engine

#### キー一覧

```console
vault kv list <secret-engine-name>
```

#### データ保存

データはkey-value形式。ややこしいけど。

```console
vault kv put -mount=<secret-engine-name> <key-name> key1=value1 key2=value2 ...
```

これも一緒

```console
vault kv put <secret-engine-name>/<key-name> key1=value1 key2=value2 ...
```

繰り返し実行すればversion更新される。

#### データ参照

```console
vault kv get -mount=<secret-engine-name> <key-name>
```

これも一緒

```console
vault kv get <secret-engine-name>/<key-name>
```

RESTであれば

```console
curl -H "X-Vault-Token: ${token}" http://server:port/v1/<secret-engine-name>/data/<key-name>
```

`curl -H "X-Vault-Token: ${token}" http://192.168.0.75:8200/v1/kv/data/zzz?version=1`とか。  
[KV - Secrets Engines - HTTP API | Vault | HashiCorp Developer](https://developer.hashicorp.com/vault/api-docs/secret/kv/kv-v2)

## auth

### 一覧

```console
vault auth list
```

### token確認

```console
vault token lookup
```

### role

#### リスト

`path`は`vault auth list`の出力で確認できる。

```console
vault list auth/path/role
```

#### 内容確認(policyなど)

```console
vault read auth/path/role/key
```

#### 削除

```console
vault delete auth/path/role/key
```

## identity/entity

### ID一覧

```console
vault list identity/entity/id
```

```console
vault list identity/entity-alias/id
```

## policy

### 一覧

```console
vault policy list
```

## hvac

Hashicorp Vault用Pythonライブラリ

- [hvac documentation](https://python-hvac.org/en/latest/overview.html)
- [hvac/hvac: :lock: Python 3.X client for HashiCorp Vault](https://github.com/hvac/hvac)

### コンストラクタ

証明書検証オフは`verify=False`を加えればOK

```py
import urllib3
import os
import hvac

urllib3.disable_warnings()

client = hvac.Client(
    url='https://vault.example.org',
    token=os.environ['VAULT_TOKEN'],
    verify=False
)
```

### OIDC

[JWT/OIDC — hvac 2.3.0 documentation](https://python-hvac.org/en/stable/usage/auth_methods/jwt-oidc.html)
