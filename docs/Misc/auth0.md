# Auth0

## 料金

[価格 - Auth0](https://auth0.com/jp/pricing)

「Free」であればクレジットカード登録も不要

## アカウント作成

「無料トライアル」でサインアップすれば「Free」プランでアカウントを作成できる。  
アカウント作成中に、米国のデータリージョンにテナント名が自動で割り当てられるが、変更する場合はチェックを入れて進める。その場合Japanを含むいくつかのリージョンから選択可能。

### 初期状態

- Applications
    - Default App
- APIs
    - Auth0 Management API
- Database
    - Username-Password-Authentication
- Social Connections
    - google-oauth2

## Application

### 作成済みアプリケーションのPermission(scope)変更

APIsタブで認可してるAPIの`∨(expand)`押下で選択画面が表示される。

## API

### 認証トークン

[Get Management API Access Tokens for Testing](https://auth0.com/docs/secure/tokens/access-tokens/management-api-access-tokens/get-management-api-access-tokens-for-testing)

Applications > APIs > Auth0 Management API > API Explorer tabから。  
初期状態ではAPI Explorerを使うためのテストアプリケーションが存在しないため、作成ボタンを押下する。  
(作成すると、「API Explorer Application」がApplications以下へ作成される)

デフォルトのトークン期限は86400秒

プロダクション向けの処理で、web画面でなくAPIを使ってトークンを取得するには以下。  
[Get Management API Access Tokens for Production](https://auth0.com/docs/secure/tokens/access-tokens/management-api-access-tokens/get-management-api-access-tokens-for-production)

権限を付与した「Machine-to-Machine Applications」のクライアントIDとSecretを使用し、以下のRESTでトークンを取得できる。

```command
curl -H 'content-type: application/x-www-form-urlencoded' \
  -X POST \
  --data grant_type=client_credentials \
  --data "client_id="${AUTH0_CLIENT_ID} \
  --data "client_secret=${AUTH0_CLIENT_SECRET}" \
  --data "audience=https://YOUR-DOMAIN/api/v2/" \
  https://YOUR-DOMAIN/oauth/token
```

レスポンスは以下の通り。

```json
{
    "access_token": "eyJ...",
    "scope": "create:client_grants update:client_grants",
    "expires_in": 86400,
    "token_type": "Bearer"
}
```

### Application

endpointは`/api/v2/clients`

#### get

[Get clients | Auth0 Management API v2](https://auth0.com/docs/api/management/v2/clients/get-clients)

```console
curl -X GET \
  -H 'Accept: application/json' \
  -H "Authorization: Bearer ${AUTH0_TOKEN}" \
  'https://YOUR-DOMAIN/api/v2/clients'
```

これで全てのApplicationをリスト形式で取得できる。  
Application名の一覧であれば例えば`jq`を使って以下。

```console
curl -X GET \
  -H 'Accept: application/json' \
  -H "Authorization: Bearer ${AUTH0_TOKEN}" \
  'https://YOUR-DOMAIN/api/v2/clients' | jq '.[].name'
```

ページング処理はqueryで指定。

```console
curl -X GET \
  -H 'Accept: application/json' \
  -H "Authorization: Bearer ${AUTH0_TOKEN}" \
  'https://YOUR-DOMAIN/api/v2/clients?page=1&per_page=4' | jq '.[].name'
```

`page`の指定は`per_page`もセットしておく必要がある。(無い場合は無視される)  
`per_page`で指定可能なのは100が最大値。101件以上ある場合はページング処理が必須。

#### create

[Create a client | Auth0 Management API v2](https://auth0.com/docs/api/management/v2/clients/post-clients)

```command
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -H "Authorization: Bearer ${AUTH0_TOKEN}" \
  -d '{"name":"rest-sample-02", "app_type": "regular_web"}' \
  'https://YOUR-DOMAIN/api/v2/clients'
```

#### delete

[Delete a client | Auth0 Management API v2](https://auth0.com/docs/api/management/v2/clients/delete-clients-by-id)

```command
curl -X DELETE \
  -H "Authorization: Bearer ${AUTH0_TOKEN}" \
  'https://YOUR-DOMAIN/api/v2/clients/CLIENT-ID'
```

成功すればレスポンスは204でボディは空

## Python Library

- [auth0/auth0-python: Auth0 SDK for Python](https://github.com/auth0/auth0-python)
- [Auth0-Python documentation — auth0-python 4.4.0 documentation](https://auth0-python.readthedocs.io/en/latest/)

### 認証トークン

```python
from auth0.authentication import GetToken

GetToken = GetToken(auth_domain, auth_client_id, client_secret=auth_client_secret)
token = GetToken.client_credentials('https://{}/api/v2/'.format(auth_domain))
print(token['access_token'])
```
