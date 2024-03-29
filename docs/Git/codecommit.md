# CodeCommit

## IAMユーザーの認証情報

### IAMユーザー作成

[Setup for HTTPS users using Git credentials - AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html)

- IAMユーザー作成
    - オプション:AWSマネジメントコンソールへのユーザーアクセスを提供は無し
- 許可のオプション
    - 「ポリシーを直接アタッチする」を選択し、「許可ポリシー」から「AWSCodeCommitPowerUser」を選択(利用形態に合わせてFullAccess/ReadOnlyに変更)

### HTTPS認証情報作成

IAMのページの「AWS CodeCommit の HTTPS Git 認証情報」で「認証情報を生成」押下。  
表示されるユーザー名とパスワードでCodeCommitのリポジトリへ認証できる。  
忘れないように認証情報をダウンロードしておくと良い。(CSV形式)

### SSHの認証と接続設定

[Linux、macOS、または Unix での AWS CodeCommit リポジトリへの SSH 接続の設定手順 - AWS CodeCommit](https://docs.aws.amazon.com/ja_jp/codecommit/latest/userguide/setting-up-ssh-unixes.html)

SSHキーペアを作成し公開鍵をIAMコンソールの「AWS CodeCommit の SSH 公開キー」へアップロード。  
「SSHキーID」をSSHアクセス時のユーザー名として`$HOME/.ssh/config`に接続設定を行う。

```config
Host git-codecommit.*.amazonaws.com
    User APK......
    IdentityFile ~/.ssh/......
```

## ブランチの削除

メニューの[リポジトリ]->[ブランチ]で、対象をチェックして「ブランチの削除」
