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

Applications > APIs > Auth0 Management API > API Explorer tabから。  
初期状態ではAPI Explorerを使うためのテストアプリケーションが存在しないため、作成ボタンを押下する。  
(作成すると、「API Explorer Application」がApplications以下へ作成される)

デフォルトのトークン期限は86400秒

