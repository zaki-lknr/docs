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
