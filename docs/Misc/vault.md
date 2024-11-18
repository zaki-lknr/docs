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
