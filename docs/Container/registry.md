# Registry

## API

### リポジトリ一覧(イメージ一覧)

```console
curl https://registry.example.org/v2/_catalog
```

### イメージのタグ一覧

リポジトリ一覧でヒットしたリポジトリ名をパスに含める

```console
curl https://registry.example.org/v2/<repository-name>/tags/list
```

## SaaS

### GitHub Container Registry

手順は以下。  
[コンテナレジストリの利用 - GitHub Docs](https://docs.github.com/ja/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

認証に使用するパスワードは、個人用アクセストークンを作成する。  
[個人アクセストークンを使用する - GitHub Docs](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

[Personal Access Tokens (Classic)](https://github.com/settings/tokens)で[Generate new token]押下、Select scopesの`write:packages`にチェックして[Generate token]押下すればトークンが生成される。  
GitHubのユーザー名と、入手したトークンをパスワードとして、

```console
$ docker login ghcr.io
```

すれば認証できる。

あとは`ghcr.io/zaki-lknr/squid:5.0.4-alpine-3.13`とかの名前のタグをつければ`push`もできる。  
pushされたイメージは、GitHubのweb画面の「Packages」で確認できる。デフォルトはprivate設定。

### ECR (Elastic Container Registry)

認証用パスワードは `aws` CLIを使用。  
`aws ecr get-login-password` を実行するとレジストリ認証に使用するパスワードを取得できる。

```console
$ aws ecr get-login-password --region ap-northeast-1
eyJwYXls ......
```

ログイン時のユーザー名は `AWS` 固定のため、レジストリログイン用のコマンドは以下の通り。

```console
$ podman login <registry URL> -u AWS -p $(aws ecr get-login-password)
```

[プライベートレジストリの認証 - Amazon ECR](https://docs.aws.amazon.com/ja_jp/AmazonECR/latest/userguide/registry_auth.html)

### ACR

ポータルからの場合は、レジストリメニューの「トークン」から、「追加」でトークンを作成する。スコープマップに権限を入力。  
作成したトークンの「詳細」からパスワードを生成できる。  
生成されるのはj`docker login`で指定するパスワード、およびDockerコマンド列全体(`docker login -u ... -p ...`)も得られる。(ユーザー名はトークン名になる)
