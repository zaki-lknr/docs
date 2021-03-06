# Git設定

## 現在の設定確認

### 現在適用される設定全て

```
$ git config -l
```

### セクションごとの設定内容

```
$ git config --system -l
$ git config --global -l
$ git config --local -l
```

## Git+SSH

### .ssh/config

鍵を指定する場合はこんな感じ。

```
Host gitlab-ce.example.org
    IdentityFile ~/.ssh/id_rsa_git

Host github.com
    IdentityFile ~/.ssh/id_rsa_git
```

cloneのパスが`git@github.com/...`とユーザー名が指定されているので、`User git`は不要

Backlogも同様にclone用文字列コピーにユーザー名含んでいるので以下でOK

```
Host <space>.git.backlog.com
    IdentityFile ~/.ssh/id_rsa_git
```

## リモートリポジトリ

### 現在の設定

```
$ git remote -v
```

### リポジトリを設定

GitHubとかでREADME無しでリポジトリ作るとコマンドが表示されるアレ

```
$ git remote add origin <URI of Repository>
```

### リモートリポジトリを変更

originをGitHubからプライベートGitLabに変更、とか。

```
$ git remote set-url origin https://my-gitlab.example.org/user/repository.git
```

HTTPS -> SSHもこれ

```
$ git remote set-url origin git@github.com:zaki-lknr/ansible-sample.git
```

### 別のリポジトリを別名で追加

```
$ git remote add hoge <other repository>
```

## git prompt

```console
curl -L https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh -o .git-prompt.sh
chmod 755 .git-prompt.sh
```

.bashrcへ以下追加

```sh
[ -f ~/.git-prompt.sh ] && source ~/.git-prompt.sh
```

この状態で、`PS1`環境変数を設定する。  
設定例

```sh
# prompt
PS1='[\u@\h \W$(__git_ps1 " (%s)")]\$ '
```

既存の`PS1`設定に追加するのであればこんな感じが良いかも。

```sh
if [ -f ~/.git-prompt.sh ]; then
    source ~/.git-prompt.sh
    PS1='$(__git_ps1 "(%s) ")'"$PS1"
fi
```
