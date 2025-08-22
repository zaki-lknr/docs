# Ansible Tower / AAP

## docs

- 旧ドキュメント
    - [1. Overview — Automation Controller User Guide v4.5](https://docs.ansible.com/automation-controller/latest/html/userguide/overview.html)
    - [1. Red Hat Ansible Automation Platform Controller Licensing, Updates, and Support — Automation Controller Administration Guide v4.5](https://docs.ansible.com/automation-controller/latest/html/administration/license-support.html)

- 現行(v2.4)
    - [Automation Controller ユーザーガイド | Red Hat Product Documentation](https://docs.redhat.com/ja/documentation/red_hat_ansible_automation_platform/2.4/html-single/automation_controller_user_guide/index)
    - [Automation Controller 管理ガイド | Red Hat Product Documentation](https://docs.redhat.com/ja/documentation/red_hat_ansible_automation_platform/2.4/html-single/automation_controller_administration_guide/index)

## ライフサイクルとバージョン

[Red Hat Ansible Automation Platform のライフサイクル - Red Hat Customer Portal](https://access.redhat.com/ja/support/policy/updates/ansible-automation-platform)

| AAP ver | ansible-core | Controller ver |
| ------- | ------------ | -------------- |
| 2.5     | 2.16         | 4.6            |
| 2.4     | 2.15         | 4.4            |
| 2.3     | 2.14         | 4.3            |
| 2.2     | 2.13         | 4.2            |
| 2.1     | 2.12         | 4.1            |

## バックアップとリストア

バックアップするには以下。  
Towerは実行中で良い。  
バックアップに成功すると、カレントに `tower-backup-YYYY-mm-dd-HH:MM:SS.tar.gz` が作成される。(symlinkの`tower-backup-latest.tar.gz`も作成される)

```console
# ./setup.sh -b
```

リストアは以下。

```console
# ./setup.sh -r -e restore_backup_file=/path/to/tower-backup-....tar.gz
```

- ver 2.4 [Chapter 27. Backup and restore | Red Hat Product Documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/controller-backup-and-restore#controller-backup-and-restore)
- ver 2.5 [Chapter 15. Backup and restore | Red Hat Product Documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.5/html/configuring_automation_execution/controller-backup-and-restore)

## アンインストール

[How to uninstall Red Hat Ansible Controller(Ansible Automation Platform 2.x)? - Red Hat Customer Portal](https://access.redhat.com/solutions/6733721)

## プロジェクト設定

### SCMを使わない場合

Gitとか使わずTowerのホストのファイルシステム上のplaybookを使う場合は `/var/lib/awx/projects` 以下にプロジェクトのディレクトリを作り、その配下にplaybook類を配置する。

たとえば `/var/lib/awx/projects/sample-demo/playbook.yml` など。

## ログ

[第11章 Automation Controller ログファイル | Red Hat Product Documentation](https://docs.redhat.com/ja/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/assembly-controller-log-files)

- /var/log/tower/
- /var/log/supervisor/
- /var/log/nginx/
- /var/lib/pgsql/data/pg_log/
- /var/log/redis/

## 設定

### キーとデフォルト値のコード

AWXの`defaults.py`である程度確認できる。

[awx/awx/settings/defaults.py at devel · ansible/awx](https://github.com/ansible/awx/blob/devel/awx/settings/defaults.py)

### TOWER_URL_BASE

通知時のホスト名

### EVENT_STDOUT_MAX_BYTES_DISPLAY

ジョブストリームに表示されるログのバイト数。超過分は省略表示される。

### MAX_EVENT_RES_DATA

大きなサイズのイベントデータの上限値。超過分は保存時に切り捨てられる。

- [3.7. Automation Controller のチューニング | Red Hat Product Documentation](https://docs.redhat.com/ja/documentation/red_hat_ansible_automation_platform/2.5/html/configuring_automation_execution/controller-tuning)
- [Returned JSON is empty when standard output is large · Issue #1789 · ansible/awx](https://github.com/ansible/awx/issues/1789)

## エラー

- [Ansible Automation Platform のトラブルシューティング | Red Hat Ansible Automation Platform | 2.5 | Red Hat Documentation](https://docs.redhat.com/ja/documentation/red_hat_ansible_automation_platform/2.5/html/troubleshooting_ansible_automation_platform/index)

### ジョブ

#### `Task was marked as running but was not present in the job queue, so it has been marked as failed.`

ジョブを起動したがキューになく処理できずに失敗。

- [Task was marked as running but was not present in the job queue, so it has been marked as failed. · Issue #14277 · ansible/awx](https://github.com/ansible/awx/issues/14277)
- [Task was marked as running in Tower but was not present in the job queue, so it has been marked as failed · Issue #9594 · ansible/awx](https://github.com/ansible/awx/issues/9594)

システムの高負荷など様々な原因が考えられる。

下記でリソース制限できるかもしれない

- `SYSTEM_TASK_ABS_MEM`
- `SYSTEM_TASK_ABS_CPU`

参考情報

- [2.3. Automation Controller 設定を使用した代替方法での容量制限 | Operator 環境のパフォーマンスに関する考慮事項 | Red Hat Ansible Automation Platform | 2.5 | Red Hat Documentation](https://docs.redhat.com/ja/documentation/red_hat_ansible_automation_platform/2.5/html/performance_considerations_for_operator_environments/con-alternative-capacity-limits_performance-considerations)
- [Unable to run 200 concurrent jobs in AWX with HA enabled. Observing one AWX instance crashes. · Issue #13657 · ansible/awx](https://github.com/ansible/awx/issues/13657)
- [Set default capacity_adjustment instance value. - Archives / AWX Project - Ansible](https://forum.ansible.com/t/set-default-capacity-adjustment-instance-value/37305/3)

## Tools

### awx-manage

#### ジョブ削除

```console
awx-manage cleanup_jobs [--help]
```

`--dry-run`で実行すれば削除対象を確認できる。  
デフォルト90日以前が削除対象。

日付指定は`--days`で指定。

```console
awx-manage cleanup_jobs --days 3
```

## API

API一覧は以下。  
~~[11. Controller API Reference Guide — Automation Controller API Guide v4.2.1](https://docs.ansible.com/automation-controller/latest/html/controllerapi/api_ref.html)~~

- [Ansible Automation Platform controller API](https://developers.redhat.com/api-catalog/api/ansible-automation-controller)
- [11. Controller API Reference Guide — Automation Controller API Guide v4.5](https://docs.ansible.com/automation-controller/4.5/html/towerapi/api_ref.html)
- [12. AWX API Reference Guide — Ansible AWX community documentation](https://ansible.readthedocs.io/projects/awx/en/latest/rest_api/api_ref.html)

稼働中のAAP/AWXのAPI・エンドポイントを確認するには、ブラウザで`/api/v2/`にアクセスすれば確認できる。

### 認証

#### ユーザー名とパスワード

webログイン時に使用するユーザー名とパスワードでRESTを実行するにはBasic認証を使用する。  
`curl`であれば`-u`オプション。

```console
curl -u $username:$password -sk https://192.168.0.27/api/v2/job_templates/
```

#### パーソナルトークン

Towerのユーザーページで、対象ユーザーを選択、「トークン」押下して「＋」押下で作成できる。  
権限はread/writeのみ。

GUIで作ったパーソナルトークンを使ったRESTは以下のように`Authorization: Bearer {token-string}`をヘッダに追加すればOK

```console
$ export tower_token=........
$ curl -sk https://192.168.0.27/api/v2/job_templates/ -H "Authorization: Bearer $tower_token"
```

[19.2. パーソナルアクセストークン (PAT) 向けの OAuth2 トークンシステムの使用](https://docs.ansible.com/ansible-tower/latest/html_ja/administration/oauth2_token_auth.html#using-oauth-2-token-system-for-personal-access-tokens-pat)

### ping

```console
$ curl -sk https://server/api/v2/ping/
```

### ジョブ

#### 一覧

```console
$ curl -sk https://192.168.0.27/api/v2/jobs/?page_size=200
```

ソートするには`order_by`を使う。キーは`-`を先頭につけると逆順。  
以下で最新のジョブが先頭に来る。

```console
$ curl -sk "https://192.168.0.27/api/v2/jobs/?page_size=200&order_by=-created"
```

`page_size`に指定できるのはMAX200？(オーバー時は無視されて200扱い)

#### 詳細

```console
curl -sk https://192.168.0.27/api/v2/jobs/{JOB_ID}
```

#### 削除

```console
curl -XDELETE -sk https://192.168.0.27/api/v2/jobs/{JOB_ID}
```

### ジョブテンプレート

#### 一覧

```console
$ curl -sk https://192.168.0.27/api/v2/job_templates/?page_size=1000 | python -m json.tool
```

#### 詳細

```console
$ curl -sk https://192.168.0.27/api/v2/job_templates/{template-ID}/
```

#### テンプレートの検索

```console
curl https://server/api/v2/job_templates/?search=Demo+Job+Template
```

#### ジョブの起動

```console
curl -XPost https://server/api/v2/job_templates/${TEMPLATE_ID}/launch/
```

### ワークフジョブローテンプレート

ジョブテンプレートとは別

#### 一覧

```console
curl https://tower.example.org/api/v2/workflow_job_templates/
```

### チーム

#### チーム一覧

```console
curl -XGET https://server/api/v2/teams/
```

### 設定のcategory_slug確認

「Settings」にある `/api/v2/settings/` で確認できる。

```console
curl -sk https://tower.example.org/api/v2/settings/ | python3 -m json.tool
{
    "count": 20,
    "next": null,
    "previous": null,
    "results": [
        {
            "url": "/api/v2/settings/all/",
            "slug": "all",
            "name": "All"
        },
        {
            "url": "/api/v2/settings/authentication/",
            "slug": "authentication",
            "name": "Authentication"
        },
        {
            "url": "/api/v2/settings/azuread-oauth2/",
            "slug": "azuread-oauth2",
            "name": "Azure AD OAuth2"
        },
        {
            "url": "/api/v2/settings/changed/",
            "slug": "changed",
            "name": "Changed"
        },
        {
            "url": "/api/v2/settings/github/",
            "slug": "github",
            "name": "GitHub OAuth2"
        },
        {
            "url": "/api/v2/settings/github-enterprise/",
            "slug": "github-enterprise",
            "name": "GitHub Enterprise OAuth2"
        },
        {
            "url": "/api/v2/settings/github-enterprise-org/",
            "slug": "github-enterprise-org",
            "name": "GitHub Enterprise Organization OAuth2"
        },
        {
            "url": "/api/v2/settings/github-enterprise-team/",
            "slug": "github-enterprise-team",
            "name": "GitHub Enterprise Team OAuth2"
        },
        {
            "url": "/api/v2/settings/github-org/",
            "slug": "github-org",
            "name": "GitHub Organization OAuth2"
        },
        {
            "url": "/api/v2/settings/github-team/",
            "slug": "github-team",
            "name": "GitHub Team OAuth2"
        },
        {
            "url": "/api/v2/settings/google-oauth2/",
            "slug": "google-oauth2",
            "name": "Google OAuth2"
        },
        {
            "url": "/api/v2/settings/jobs/",
            "slug": "jobs",
            "name": "Jobs"
        },
        {
            "url": "/api/v2/settings/ldap/",
            "slug": "ldap",
            "name": "LDAP"
        },
        {
            "url": "/api/v2/settings/logging/",
            "slug": "logging",
            "name": "Logging"
        },
        {
            "url": "/api/v2/settings/named-url/",
            "slug": "named-url",
            "name": "Named URL"
        },
        {
            "url": "/api/v2/settings/radius/",
            "slug": "radius",
            "name": "RADIUS"
        },
        {
            "url": "/api/v2/settings/saml/",
            "slug": "saml",
            "name": "SAML"
        },
        {
            "url": "/api/v2/settings/system/",
            "slug": "system",
            "name": "System"
        },
        {
            "url": "/api/v2/settings/tacacsplus/",
            "slug": "tacacsplus",
            "name": "TACACS+"
        },
        {
            "url": "/api/v2/settings/ui/",
            "slug": "ui",
            "name": "UI"
        }
    ]
}
```

### その他のシステム設定

category_slugに`system`を指定。  
キー一覧は以下の通り

```console
$ curl -sk https://tower.example.org/api/v2/settings/system/ ... | python3 -m json.tool
{
    "ACTIVITY_STREAM_ENABLED": true,
    "ACTIVITY_STREAM_ENABLED_FOR_INVENTORY_SYNC": false,
    "ORG_ADMINS_CAN_SEE_ALL_USERS": true,
    "MANAGE_ORGANIZATION_AUTH": true,
    "TOWER_URL_BASE": "https://127.0.0.1",
    "REMOTE_HOST_HEADERS": [
        "REMOTE_ADDR",
        "REMOTE_HOST"
    ],
    "PROXY_IP_ALLOWED_LIST": [],
    "LICENSE": {
        ...
    },
    "REDHAT_USERNAME": "",
    "REDHAT_PASSWORD": "",
    "SUBSCRIPTIONS_USERNAME": "",
    "SUBSCRIPTIONS_PASSWORD": "",
    "AUTOMATION_ANALYTICS_URL": "https://cloud.redhat.com/api/ingress/v1/upload",
    "INSTALL_UUID": "...",
    "DEFAULT_CONTROL_PLANE_QUEUE_NAME": "controlplane",
    "DEFAULT_EXECUTION_QUEUE_NAME": "default",
    "DEFAULT_EXECUTION_ENVIRONMENT": null,
    "CUSTOM_VENV_PATHS": [],
    "INSIGHTS_TRACKING_STATE": false,
    "AUTOMATION_ANALYTICS_LAST_GATHER": null,
    "AUTOMATION_ANALYTICS_LAST_ENTRIES": "",
    "AUTOMATION_ANALYTICS_GATHER_INTERVAL": 14400,
    "IS_K8S": false
}
```
