# Git設定

## ユーザー名・メールアドレスの設定

```console
git config --global user.name zaki
git config --global user.email zaki.hmkc@...
```

### 共用アカウントなどで$HOMEに記録したくない場合

`--local`を使えば、`$HOME/.gitconfig`でなく、現在のリポジトリの設定に記録される。

```console
git config --local user.name zaki
git config --local user.email zaki.hmkc@...
```

### 環境変数を使う場合

以下を設定することで、`user.name`と`user.email`を上書きできる。  
共用アカウントで誰かが`$HOME/.gitconfig`を設定してしまい、自分のワークディレクトリの`.gitconfig`の設定忘れでの事故防止的にはこちらが良いかも。

```sh
export GIT_AUTHOR_NAME
export GIT_AUTHOR_EMAIL
export GIT_COMMITTER_NAME
export GIT_COMMITTER_EMAIL
```

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

### 共用アカウントなどで$HOME/.ssh/config以外を使いたい場合

以下設定。

```sh
export GIT_SSH_COMMAND="ssh -F /path/to/myconf/ssh/config"
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

## gitconfig

### コマンドのalias

`git st`で`git status`にしたい場合はこんな感じ

```ini
[user]
	email = ...
	name = ...
[alias]
	st = status
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
    PS1='\[\e[31m\]$(__git_ps1 "(%s) ")\[\e[0m\]'"$PS1"
    export GIT_PS1_SHOWDIRTYSTATE=true
    export GIT_PS1_SHOWUNTRACKEDFILES=true
    export GIT_PS1_SHOWSTASHSTATE=true
    export GIT_PS1_SHOWUPSTREAM=auto
fi
```
