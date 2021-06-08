# Ansible Tower

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

