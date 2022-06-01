# Ansible Tower

## プロジェクト

### SCMを使わない場合

Gitとか使わずTowerのホストのファイルシステム上のplaybookを使う場合は `/var/lib/awx/projects` 以下にプロジェクトのディレクトリを作り、その配下にplaybook類を配置する。

たとえば `/var/lib/awx/projects/sample-demo/playbook.yml` など。

## API

### 認証

#### パーソナルトークン作成

Towerのユーザーページで、対象ユーザーを選択、「トークン」押下して「＋」押下で作成できる。  
権限はread/writeのみ。

GUIで作ったパーソナルトークンを使ったRESTは以下のように`Authorization: Bearer {token-string}`をヘッダに追加すればOK

```console
$ export tower_token=........
$ curl -sk https://192.168.0.27/api/v2/job_templates/ -H "Authorization: Bearer $tower_token"
```

[19.2. パーソナルアクセストークン (PAT) 向けの OAuth2 トークンシステムの使用](https://docs.ansible.com/ansible-tower/latest/html_ja/administration/oauth2_token_auth.html#using-oauth-2-token-system-for-personal-access-tokens-pat)

### ジョブテンプレート一覧

```console
$ curl -sk https://192.168.0.27/api/v2/job_templates/?page_size=1000 | python -m json.tool
```
