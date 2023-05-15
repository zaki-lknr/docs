# CodeCommit

## IAMユーザーでHTTPSでclone

### IAMユーザー作成

[Setup for HTTPS users using Git credentials - AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html)

- IAMユーザー作成
    - オプション:AWSマネジメントコンソールへのユーザーアクセスを提供は無し
- 許可のオプション
    - 「ポリシーを直接アタッチする」を選択し、「許可ポリシー」から「AWSCodeCommitPowerUser」を選択(利用形態に合わせてFullAccess/ReadOnlyに変更)

### 認証情報作成

IAMのページの「AWS CodeCommit の HTTPS Git 認証情報」で「認証情報を生成」押下。  
表示されるユーザー名とパスワードでCodeCommitのリポジトリへ認証できる。  
忘れないように認証情報をダウンロードしておくと良い。(CSV形式)
