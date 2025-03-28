# GitHub

## リポジトリ

### デフォルトブランチの変更

リポジトリの「Settings」->「Default branch」で対象を選択する。

[デフォルトブランチを変更する - GitHub Docs](https://docs.github.com/ja/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/changing-the-default-branch)

### ライセンスファイルの追加

[リポジトリへのライセンスの追加 - GitHub Docs](https://docs.github.com/ja/communities/setting-up-your-project-for-healthy-contributions/adding-a-license-to-a-repository)

「新規ファイル追加」でファイル名`LICENSE`を追加しようとするとテンプレートを選択するボタンが表示される。

## ブランチ

### ブランチ作成

[リポジトリ内でブランチを作成および削除する - GitHub Docs](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-and-deleting-branches-within-your-repository)

GitHub上でブランチを作成するには「View all branches」画面で「New branch」押下する。  
あるいはブランチ選択のドロップダウンで作成したいブランチ名を入力してCreateを押下。

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

## 書式

### 自動リンク

[自動リンクされた参照と URL - GitHub Docs](https://docs.github.com/ja/get-started/writing-on-github/working-with-advanced-formatting/autolinked-references-and-urls)

## PR

### issueとの関連付け

[Pull RequestをIssueにリンクする - GitHub Docs](https://docs.github.com/ja/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue)

#### マージで自動クローズするキーワード

- close
- closes
- closed
- fix
- fixes
- fixed
- resolve
- resolves
- resolved

### レビュー

- [プルリクエストで提案された変更をレビューする - GitHub Docs](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/reviewing-proposed-changes-in-a-pull-request)

コメント入力のあとに[Submit review]を忘れないこと。

- Request changes / 追加修正がある場合
- Approve / 問題ない場合
- Comment / その他

### suggested change

その場で修正コミットを送るには、対象行を選択して「Commit suggestion」押下、テキストフィールドに`suggestion`というコードブロックが入るので、その内容を正しいものに修正する

[プルリクエストへのコメント - GitHub Docs](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/commenting-on-a-pull-request)

修正を取り込む側は「Commit Suggestion」押下してコミットメッセージを入力して反映する。

[プルリクエストでのフィードバックを取り込む - GitHub Docs](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/incorporating-feedback-in-your-pull-request)

### PRマージ時のブランチ自動削除

リポジトリの設定から。

[ブランチの自動的削除を管理する - GitHub Docs](https://docs.github.com/ja/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-the-automatic-deletion-of-branches)

### PR作成後にコミットを追加したい場合

PRがopenのままリジェクトとかされてないなら、追いpushすれば自動的に反映される。

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

## GitHub Actions

### ワークフローの手動実行

<https://docs.github.com/ja/actions/managing-workflow-runs-and-deployments/managing-workflow-runs/manually-running-a-workflow>

### ワークフローとジョブの再実行

ワークフロー実行結果一覧から指定のワークフローを開き、画面右上の「Re-run all jobs」ボタンを押下ｋ
