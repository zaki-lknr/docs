# Git基本操作

[gist](https://gist.github.com/zaki-lknr/5f323de8383baa41bda4839362d4c3a5)から引っ越し。

## git clone

### branchを指定

```
$ git clone -b <branch-name> <repository-URL>
```

tagの場合も同じように

```
$ git clone -b <tag-name> <repository>
```

### 出力先ディレクトリ指定

`-l <dirname>`を付ける

```
$ git clone -b <branch-name> <repository-URL> -l app-src-work
```

実は`-l`は無くてもよい。

```console
$ git clone <repository-url> <local-path>
```

## ローカルリポジトリ作成

リポジトリ用ディレクトリを作ってそのディレクトリ上で`git init`して`README.md`をinitial commitする。

```
$ git init
$ echo "# this is hogehgoe repository" >> README.md
$ git add README.md
$ git commit -m "first commit"
```

## リモートリポジトリ作成

### ssh

```console
$ cd /opt/repos
$ git init --bare sample.git
```

これで `/opt/repos/sample.git` リポジトリが出来る。

```console
$ cd ${workdir}
$ git clone ssh://localhost/opt/repos/sample.git
```

これでワークディレクトリにcloneできる。  
この場合対象の書き込み権限は必要。

この`git init --bare <path>`は`git clone`したクローンしたリポジトリにも有効。

## commit

### まず対象をadd

```
$ git add path/file
```

### commit

```
$ git commit
```

これ実行するとエディタが起動する。
先頭`#`になってる行は全て無視されるので、任意の位置にコミットログを入力する

### optionでコミットメッセージ付きcommit

```
$ git commit -m "commit messages"
```

### amend

```console
$ git commit --amend
```

直前のコミットに内容をあとから追加できる。  
コミット漏れのファイルがあった場合などに1コミットにまとめられる。

```console
$ git commit --amend --no-edit
```

`--no-edit`を付与すれば、コミットログは前と同じものを使用できる。

## マージ

### hogehogeブランチをマージする

```
$ git merge hogehoge
```

### マージコミットを作る (--no-ff)

```
$ git merge features/add_worker_node --no-ff 
```

### rebaseでマージ

作業ブランチをmasterにmergeする前に、masterの変更点を作業ブランチでrebaseで取り込んでおく、が基本の使用法っぽい。

- [よく分かる！git rebaseとmergeの違いと使い分け | WWWクリエイターズ](https://www-creators.com/archives/1943)
- [3分で理解できる！git-rebaseとmergeとの違いまとめ | CodeCampus](https://blog.codecamp.jp/git_rebase)
- [7. rebaseでマージする｜サル先生のGit入門【プロジェクト管理ツールBacklog】](https://backlog.com/ja/git-tutorial/stepup/13/)

`merge`は「他ブランチの内容を取り込む」で、`rebase`は「他ブランチの内容を適用済みだったことにする」かな？

リモートブランチから最新を取得

```console
$ git fetch origin 
```

指定ブランチの内容を`rebase`で現在のブランチへ取り込み

```console
$ git rebase origin/hogehoge
```

変更がローカルに残ってる場合は

```console
$ git rebase origin/hogehoge
error: cannot rebase: You have unstaged changes.
error: Please commit or stash them.
```

エラーになるので、退避してrebaseする

```console
$ git stash save
$ git rebase origin/hogehoge
$ git stash pop
```

origin/mainから取り込むときに`pull`するとmergeになってしまうので、rebaseしたい場合は`fetch`する。(たぶん)

## conflictした場合

### 修正する場合は普通に

1. 修正する
2. `add`して`commit`する(普通)

(「修正する」をもう少し詳しくあとで書こう)

### mergeをやめる場合(未修正)

```
$ git merge --abort
```

マージ前の状態に戻る。

rebaseのコンフリクト時の取り消しは以下。

```
git rebase --abort
```

### rebaseのコンフリクト

`git rebase main`したらこのエラー

```console
Auto-merging <filename>
CONFLICT (content): Merge conflict in <filename>
error: could not apply e91cdab... <commit message>
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
hint: Disable this message with "git config advice.mergeConflict false"
Could not apply e91cdab... <commit message>
```

`git-prompt`を使っていると、プロンプトの表示は以下のようになる。

```console
(feature/branch *+|REBASE 1/1) 
```

コンフリクト箇所の修正と修正後の`add`は`merge`の時と同様。  
`add`した状態の`status`

```console
$ git st
interactive rebase in progress; onto a4650f8
Last command done (1 command done):
   pick e91cdab add: 24時間以内のチェックイン数の表示 #59
No commands remaining.
You are currently rebasing branch 'feature/count-24' on 'a4650f8'.
  (all conflicts fixed: run "git rebase --continue")

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   swarm.js
```

ここで`rebase`を続けるために、メッセージに出てる通り`git rebase --continue`を実行するとエディタが起動するのでコミットメッセージを編集すればrebaseが完了する。

### stash pop後のconflict

メッセージが以下のようになり`git merge --abort`は効かない。  
状態としてはステージングになっている。

```console
Unmerged paths:
  (use "git restore --staged <file>..." to unstage)
  (use "git add <file>..." to mark resolution)
        both modified:   filename
```

メッセージにある通り、コンフリクトを解決したのであれば`git add`を、変更を破棄したい場合は`git restore --staged`で`add`を取り消し、さらに`git restore`すれば変更前の状態に戻る。

## add (ステージング)

### ステージングに追加

```
$ git add <path>
```

パスはディレクトリ指定の場合は再帰的にファイルが追加される

### 行単位のadd

```console
$ git add -p
```

実行するとdiffが表示されるので、変更単位ごとに`y`で追加・`n`でスルーできる。  
変更単位を細かく分割する場合は`e`でエディタが起動するので、

- `-`の削除行を対象外にするにはスペースに変更
- `+`の追加行を対象外にするには行削除

する。

> To remove '-' lines, make them ' ' lines (context).
> To remove '+' lines, delete them.
> Lines starting with # will be removed.
> If the patch applies cleanly, the edited hunk will immediately be marked for staging.
> If it does not apply cleanly, you will be given an opportunity to

### 追加の取り消し

```
$ git reset HEAD <path>
```

### ステージングのファイルの確認

```
$ git status
```

### ステージングに追加された差分を確認

```
$ git diff --cached [file]
```

## ブランチ操作

### 今いるブランチどこ？

```
$ git branch 
  dev/calico
  devel
  features/cni_use_calico
  features/delete_node
  features/divide_playbook
  features/divide_roles
  features/separate_cni_firewalld_configure
  features/separate_parameters
  features/wait_new_node
* master
```

### ブランチ作成

```
$ git branch features/divide_playbook
```

### ブランチ切り替え

```
$ git checkout features/divide_playbook
```

Git 2.23以降は

```console
$ git switch features/divide_playbook
```

### ブランチ作成＆切り替え

```
$ git checkout -b features/divide_playbook
```

Git 2.23以降は

```console
$ git switch -c features/divide_playbook
```

### ブランチ間の差分

ブランチ名を引数に実行すればOK

```console
git diff <branch1-name> <branch2-name>
```

### ローカルブランチの削除

```
$ git branch --delete features/swapoff features/replace-yum-config-manager 
```

↑複数指定した場合

### ローカルブランチの強制削除

未マージのコミットがあったりすると削除できないけど、`-D`で強制的に削除できる

```console
$ git branch features/foobar -D
```

### ローカルブランチ名の変更

```console
# 現在のブランチ名を変更
$ git branch -m <new-branch-name>
```

```console
# 指定ブランチを変更
$ git branch -m <current-branch-name> <new-branch-name>
```

```console
# 強制変更 (GitHubのquick setupの表示だとこれ)
$ git branch -M main
# --helpを見る限り`--move --force`っぽい
```

作成済みリポジトリの`master`を`main`に変更するのもこれ。

```console
git branch -m master main
```

### ブランチをpush

```console
# 同じブランチ名でpush
$ git push origin <local-branch-name>
```

```console
# 異なるブランチ名でpush
$ git push origin <local-branch-name>:<remote-branch-name>
```

```console
# 複数ブランチまとめてpush
$ git push origin <branch-name-1> <branch-name-2>
```

### リモートのブランチ一覧

```
$ git branch -r
```

### リモートブランチをfetch

```
$ git fetch origin <remote-branch>
```

リモートのブランチをローカルの追跡ブランチに取り込む。  
これでローカルブランチに`merge`や`rebase`出来る。

### リモートブランチを削除

```console
$ git push origin :<remote-branch-name>
```

`git remote -r`で出力されるのにすでにリモートには無い場合は`git fetch --prune`で参照のないブランチは消える

```console
$ git push origin :<remote-branch-name>
error: failed to push some refs to 'github.com:******'   # 消せない
$ git fetch --prune
```

## stash

### 一覧

stashに退避している内容の一覧

```console
git stash list
```

### 退避

現在の未コミットの変更をstashへ退避

```console
git stash save
```

### 名前付きの退避

```console
git stash save comment
```

`list`実行時にコメントが表示される。

### 退避データをもとに戻す

```console
git stash pop
```

### 退避データの詳細

```console
git stash show stash@{0} -p
```

これでdiffを確認できるできる。  
`-p`無しの場合はサマリのみ。

## tag操作

タグ操作は基本的にブランチと同じ。

### tagを作成

最新コミットへtagを作成

```console
git tag -a tag-name -m 'coment'
```

### tagをpushする

デフォルトではtagはpushされない

```
$ git push --tags
```

### tagをcloneする

指定tagをcloneするのはブランチと同じ

```
$ git clone -b <tag-name> <repository>
```

### tag一覧

```
$ git tag
```

`-l`は付けても無くてもtag名一覧が表示される。

```
$ git tag -n
```

`-n`を付けるとタグのコメントも一緒に表示される。

### 指定tagにブランチ切り替え (tagをチェックアウト)

```
$ git checkout <tag-name>
```

switchの場合はオプションが必要？

```console
$ git switch --detach <tag-name>
```

## 移動

### ディレクトリを新規作成しそこへ既存ファイルを移動

```
$ mkdir dir
$ git add dir
$ git mv managed.file dir/
```

## 削除

### ファイルを削除

```
$ git rm /path/to/file
```

実ファイルも消える

### ディレクトリを削除

```
$ git rm -r /path/to/directory
```

実ファイルも消える

### バージョン管理から外すのみ

```
$ git rm --cached /path/to/file
```

Git上は削除されるがファイルシステムには残る。  
ディレクトリの場合は`-r`もつける。

---

`git rm`直後は、`git status`で削除したものが`deleted`と表示されステージング状態なので、`commit`すればリポジトリに反映される。

### Untrackedファイルを削除する

`-n`でファイルを確認

```console
$ git clean -n [path/to]
```

`-f`で削除する。

```
$ git clean -f [path/to]
```

## コミットログ

### ログを見る

```
$ git log
```

k8sのクセで`logs`って入れると怒られる

### 1行のシンプルなログで出力

```
$ git log --oneline 
```

### マージ/ブランチなどのツリーも表示

```
$ git log --oneline --graph
```

### 別ブランチのログ

```
$ git log --oneline <branch-name>
```

### マージ元のブランチのコミットは非表示

```
$ git log --first-parent
```

### コミット時の対象ファイルを表示

`pull`のように変更量などを表示

```
$ git log --stat
:
:
commit 8331291c45b99411ee14076cc0e73dddfeafa153 (HEAD -> main, origin/main, origin/HEAD)
Author: zaki-lknr <zaki.hmkc+github@gmail.com>
Date:   Wed Sep 15 16:44:39 2021 +0900

    add: ansible/directives: whenでリスト

 docs/Ansible/directives.md | 24 ++++++++++++++++++++++++
 1 file changed, 24 insertions(+)
```

`status`のときのように変更種別の表示

```
$ git log --name-status
:
:
commit 8331291c45b99411ee14076cc0e73dddfeafa153 (HEAD -> main, origin/main, origin/HEAD)
Author: zaki-lknr <zaki.hmkc+github@gmail.com>
Date:   Wed Sep 15 16:44:39 2021 +0900

    add: ansible/directives: whenでリスト

M       docs/Ansible/directives.md
```

`--oneline`との併用も可

## 差分

### 直前のコミットの差分

```console
git diff HEAD^
```

未コミットがあればそれも含めて表示される(`git diff`の結果とミックスされる)  
未コミットを除くには `git diff HEAD^ HEAD`

### ブランチ間の差分

ブランチ名を引数に実行すればOK

```console
git diff <branch1-name> <branch2-name>
```

### whitespace無視

```console
$ git diff -w
```

>        -w, --ignore-all-space
>            Ignore whitespace when comparing lines. This ignores differences
>            even if one line has whitespace where the other line has none.

### この行をコミットしたのは誰 (git blame)

これで行ごとの最終コミットのID、ユーザー名、コミット日時が出力される

```console
git blame <filename>
```

### git blameで特定コミットを除く

例えば過去に「ファイルの改行コードを変更」というコミットがあると、全ての行で変更が発生することになるため、`git blame`を使ってもそれ以前のコミットを確認できない。  
こういう時は特定のコミットを除く`--ignore-rev`オプションを使って除外したいコミットを指定する。

```console
git blame --ignore-rev <除外したいコミットID> <filename>
```

除外対象が複数ある場合は、その分指定すればOK

## サブモジュール

### カレントのリポジトリに別のリポジトリをサブモジュールとして追加

ローカルリポジトリの作業ディレクトリ内に`bsky-client`ディレクトリへ別リポジトリのソースを取得する。

```console
git submodule add https://github.com/zaki-lknr/bsky-client.git bsky-client
```

### サブモジュールを更新する

対象ディレクトリで`git pull`を実行する。  
親リポジトリで`git diff`すれば更新されていることを確認できる。

```diff
$ git diff
diff --git a/bsky-client b/bsky-client
index ae590a7..2a0550c 160000
--- a/bsky-client
+++ b/bsky-client
@@ -1 +1 @@
-Subproject commit ae590a7142b82f0cb77d4fd7b970c6ab3a9dc8fa
+Subproject commit 2a0550cfc11ca1d06f13596f2eb087cc75dcdf9b
```

## 前の状態に戻す

### 指定コミットの状態に戻す

```console
git reset --hard '対象のコミットID'
```

`reset --hard`は[コミットの取り消し](trouble/#_6)にも使用する

## その他

### HEAD

概略(語弊あるかも)

- `HEAD` は最新(カレント)
- `HEAD~` 1個前
- `HEAD~1` 同上(1は省略可能)
- `HEAD~2` 2個前

`git log`した時の、

```console
ec33978 (HEAD -> main) add: docker: ボリュームのリンク追加
bd9aa93 add: ansible/config: ansible.cfgサンプル
bc5eb56 add: git/configure: git-prompt設定後のPS1環境変数
:
```

に対して、

- `ec33978`が`HEAD`
- `bd9aa93`が`HEAD~`
- `bc5eb56`が`HEAD~2`

---

`^`と`~`は基本は同じ。  
`HEAD`の一つ前なら`HEAD^`または`HEAD~`、2つ前なら`HEAD^^`または`HEAD~2`になる。  
ただし厳密には違うもので、`^`はブランチのマージで親が複数ある場合に使用する。

> コミットを指定するときに、~(チルダ)と^(キャレット)を使ってあるコミットからの相対位置で指定することもできます。この時に、よく使われるのがHEADです。~(チルダ)を後ろに付け加えることで何世代前の親かを指定することができます。^(キャレット)は、ブランチのマージで親が複数ある場合に、何番目の親かを指定することができます。
>
> [ブランチの切り替え｜サル先生のGit入門【プロジェクト管理ツールBacklog】](https://backlog.com/ja/git-tutorial/stepup/03/)

## .gitignore

[空のディレクトリを維持するための、 .gitkeep と .gitignore の使い分け - Qiita](https://qiita.com/ndxbn/items/f124d2b183b60cb074e2)
