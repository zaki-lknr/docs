# GitHub

## 設定

### 2要素認証を設定

[2 要素認証を設定する - GitHub Docs](https://docs.github.com/ja/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication)

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
