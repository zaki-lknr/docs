# GitLab

## アップグレード情報

[Upgrading GitLab | GitLab](https://docs.gitlab.com/ee/update/)

### アップグレードパス

[Upgrade paths | GitLab Docs](https://docs.gitlab.com/update/upgrade_paths/)

バージョン確認は以下ツールがrequired upgrade stopsの一覧が確認できて便利

[Upgrade Path](https://gitlab-com.gitlab.io/support/toolbox/upgrade-path/)

### コンテナ版のアップグレード

[Upgrade | GitLab Docs](https://docs.gitlab.com/install/docker/upgrade/)

## CI/CD

### キーワードリファレンス

- [`.gitlab-ci.yml` キーワードリファレンス | GitLab](https://gitlab-docs.creationline.com/ee/ci/yaml/)
- [CI/CD YAML syntax reference | GitLab Docs](https://docs.gitlab.com/ci/yaml/)

### Actions移行

- [GitHub Actionsから移行する | GitLab Docs](https://docs.gitlab.com/ja-jp/ci/migration/github_actions/)
- [GitLab CI/CD から GitHub Actions への移行 - GitHub Enterprise Cloud Docs](https://docs.github.com/ja/enterprise-cloud@latest/actions/tutorials/migrate-to-github-actions/manual-migrations/migrate-from-gitlab-cicd)

### Runner登録

executor dockerの場合  

```console
gitlab-runner register -n \
  --url ${gitlab_url} \
  --token ${token} \
  --name container-executor \
  --executor docker \
  --docker-image ${container_image} \
  --docker-network-mode ${container_network} \
  --docker-privileged
```

最後の`--docker-privileged`がないと、dindでイメージビルドなどが失敗する。(dockerコマンドが使用不能)

```console
failed to connect to the docker API at tcp://docker:2375: lookup docker on 127.0.0.11:53: server misbehaving
```
