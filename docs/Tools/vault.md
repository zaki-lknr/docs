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

#### データ参照

```console
vault kv get -mount=<secret-engine-name> <key-name>
```
