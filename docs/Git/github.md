# GitHub

## リポジトリ

### デフォルトブランチの変更

[デフォルトブランチを変更する - GitHub Docs](https://docs.github.com/ja/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/changing-the-default-branch)

## 設定

### 2要素認証を設定

[2 要素認証を設定する - GitHub Docs](https://docs.github.com/ja/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication)

### git cloneの認証

#### HTTPS

GitHubのアカウントでは認証できないので、個人用アクセストークンを作成する。

- [コマンドラインで認証する](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/about-authentication-to-github#authenticating-with-the-command-line)
- [HTTPS URL を使ってクローンを作成する](https://docs.github.com/ja/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls)

トークンの作成は、[個人アクセストークンを使用する - GitHub Docs](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)を参照。  

Settings -> Developer settings -> Personal access tokens -> Tokens(classic)で、「Generate new token (classic)」押下し、Scopeの`repo`にチェックを入れて作成したトークンをパスワードとして使用する。  
ユーザー名はGitHubアカウント名を使用する。

## PR

### issueとの関連付け

[Pull RequestをIssueにリンクする - GitHub Docs](https://docs.github.com/ja/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue)

### レビュー

- [プルリクエストで提案された変更をレビューする - GitHub Docs](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/reviewing-proposed-changes-in-a-pull-request)

コメント入力のあとに[Submit review]を忘れないこと。

- Request changes / 追加修正がある場合
- Approve / 問題ない場合
- Comment / その他

### PRマージ時のブランチ自動削除

リポジトリの設定から。

[ブランチの自動的削除を管理する - GitHub Docs](https://docs.github.com/ja/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-the-automatic-deletion-of-branches)

## Release

[リポジトリのリリースを管理する - GitHub Docs](https://docs.github.com/ja/repositories/releasing-projects-on-github/managing-releases-in-a-repository)

### webでtag作成とrelease作成

まずリリース一覧ページへ遷移、[Draft a new release]押下し、[Choose a tag]から[Find or create a new tag]に作成するタグ名を入力してCreateする。  
[Generate release notes]押下すると、それっぽい情報が自動入力されるのでそれを編集。

下部の"Attach binaries by dropping them here or selecting them"にzipなどのバイナリファイルがあれば添付する。

あとは[Publish release]すればtagとreleaseが作成され公開される。

## API

### デフォルトブランチを取得する

`https://api.github.com/repos/{owner}/{repo}` を`GET`すると、対象リポジトリの情報を取得できる。  
JSON形式のレスポンスのうち、`default_branch`キーの値にデフォルトブランチがセットされている。

```console
$ curl https://api.github.com/repos/netbox-community/ansible_modules
{
  "id": 204365310,
  "node_id": "MDEwOlJlcG9zaXRvcnkyMDQzNjUzMTA=",
  "name": "ansible_modules",
  "full_name": "netbox-community/ansible_modules",
  "private": false,

  :
  :

  "visibility": "public",
  "forks": 123,
  "open_issues": 46,
  "watchers": 200,
  "default_branch": "devel",

  :
  :
}
```

## diff

[コミットを比較する - GitHub Docs](https://docs.github.com/ja/pull-requests/committing-changes-to-your-project/viewing-and-comparing-commits/comparing-commits)

コミットを比較するには、`<リポジトリURL>/compare/SHAコードA..SHAコードB` を開く。  
例： [Comparing 3474..faf3 · zaki-lknr/docs](https://github.com/zaki-lknr/docs/compare/3474..faf3)

同じコードを指定して`^`や`~n`も指定できる。

- <https://github.com/zaki-lknr/docs/compare/faf3~2..faf3>
- <https://github.com/zaki-lknr/docs/compare/faf3%5E%5E..faf3>
