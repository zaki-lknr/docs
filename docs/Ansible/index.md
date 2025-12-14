# 基本・ネタ帳

## コンセプト

[Ansible concepts — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/basic_concepts.html)

## リリースとメンテナンス情報

- [Releases and maintenance — Ansible Documentation](https://docs.ansible.com/ansible/devel/reference_appendices/release_and_maintenance.html)
- [Red Hat Ansible Automation Platform のライフサイクル - Red Hat Customer Portal](https://access.redhat.com/ja/support/policy/updates/ansible-automation-platform)
- [Ansible Automation Platform のライフサイクル - 赤帽エンジニアブログ](https://rheb.hatenablog.com/entry/aap_lifecycle)

### コミュニティバージョンとansible-coreバージョン

Ansible Xとansible-core zのバージョン

Release and maintenanceの中の[Ansible community changelogs](https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html#ansible-community-changelogs)

### サポートバージョンマトリクス

Release and maintenanceの中の[ansible-core support matrix](https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html#ansible-core-support-matrix)

## ロードマップ

[Ansible Roadmap — Ansible Documentation](https://docs.ansible.com/ansible/latest/roadmap/ansible_roadmap_index.html)

## ディレクトリ構成

[Sample Ansible setup — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/sample_setup.html)

「Sample directory layout」や「Alternative directory layout」を参照。

## Playbook書き方

- [Good Practices for Ansible - GPA](https://redhat-cop.github.io/automation-good-practices/)
- [Tips and tricks — Ansible Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html) 旧「ベストプラクティス」のページ。
- [Conditionals — Ansible Documentation](https://terryhowe.github.io/ansible-modules-hashivault/user_guide/playbooks_conditionals.html) 条件分

## 実行制御

- [Controlling playbook execution: strategies and more — Ansible Community Documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_strategies.html)
- [Controlling where tasks run: delegation and local actions — Ansible Community Documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_delegation.html)

## ネットワーク

- [Ansible for Network Automation — Ansible Documentation](https://docs.ansible.com/ansible/latest/network/index.html)
- [Ansible Network Examples — Ansible Documentation](https://docs.ansible.com/ansible/latest/network/user_guide/network_best_practices_2.5.html)
- [Network Debug and Troubleshooting Guide — Ansible Documentation](https://docs.ansible.com/ansible/latest/network/user_guide/network_debug_troubleshooting.html)
- [Platform Options — Ansible Documentation](https://docs.ansible.com/ansible/latest/network/user_guide/platform_index.html)

## ドキュメント

### 索引

[Index — Ansible Documentation](https://docs.ansible.com/ansible/latest/genindex.html)

### ansible-doc

```console
$ ansible-doc <module-name>
```

```console
$ ansible-doc <module-fqcn>
```

モジュールパスを指定するには

```console
$ ansible-doc -M /path/to/collection-path/ansible_collections/namespace/collectionname/plugins/modules hogehoge
```

パスが合わない場合は、エラーメッセージに「これらのパスにそんなモジュールないんだが？」とパス一覧が表示されるので、そこから調整する。

## Automation Hub

### トークンの作成

[Hub への接続](https://console.redhat.com/ansible/automation-hub/token/)

## 他プロダクトとの連携

- [HashiCorp と Ansible Automation Platform を使い始める | Red Hat Ansible Automation Platform | 2.5 | Red Hat Documentation](https://docs.redhat.com/ja/documentation/red_hat_ansible_automation_platform/2.5/html-single/getting_started_with_hashicorp_and_ansible_automation_platform/index)
