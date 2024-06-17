# やらかしたとき

## ユーザー設定前にcommitした

メッセージ出るのでその通りすればいい

```
$ git config --global user.name "Your Name"
$ git config --global user.email you@example.com
$ git commit --amend --reset-author
```

※ これは最初の1回のみ(`--amend`による直前のコミットのみ修正)

複数のcommitがある場合は一括変更も可能。  
※ 条件設定しない場合、(forkなどしてきた)他ユーザーのコミットも書き換えるので注意

[GitのCommitユーザを修正する方法 - Qiita](https://qiita.com/y10exxx/items/dcea0e39788d649ca8ba)

## ブランチに移動する前に編集してしまった(未コミット状態)

`stash save`で変更を退避 -> ブランチに移動 -> `stash pop`で退避した変更点を取り出す

```
$ git stash save
$ git checkout <branch-name>
$ git stash pop
```

## --amendを忘れてcommit

未コミットの変更でなく、コミット済みAとBをまとめるには`git rebase`を使う
直前のコミット二つであれば

```console
git rebase -i HEAD^^
```

でエディタが起動するので2行目(直近の新しい方)のコミットを`pick`から`squash`に変更して保存、コミットメッセージ編集のエディタになるので内容を編集して終了する。

## 過去のコミットに対する打ち消しコミット

コミットログ上は「過去コミット」「打ち消しコミット」が残るもの。  
(※ コミットしたことそのものを取り消したい場合は`git reset`を使う)

```
$ git revert <取り消したいコミットID>
```

範囲指定も可能

```console
$ git revert HEAD~5..HEAD~3
```

対象コミット後に変更があったりするとコンフリクトになるので、手動で修正し、`add`,`commit`する。

## コミットログの変更

### 直近のコミットのコミットログ変更

```
$ git commit --amend
```

### 2つ以上前のコミットのコミットログを変更

3つ前の場合は

```
$ git rebase -i HEAD~3
```

これでエディタが起動してどのコミットを変更するかの選択になるので、対象の行頭の`pick`を`edit`に変更して`:wq`する。  
この状態で

```
$ git commit --amend
```

を実行すると、`edit`対象のコミットログに対しての再コミットとなるので、コミットログを修正する。  
修正したら

```
$ git rebase --continue
```

対象が複数ある場合は次のコミットログを`git commit --amend`して`git rebase --continue`を件数分繰り返し。

最後の`rebase --continue`を実行すると完了する。  

## コミットの取り消し

間違えてコミットした時点で「あ」と思ったら

### git reset --soft HEAD^

`git commit`する前、`git add`した後の状態に戻す

### git reset --mixed HEAD^

`git add`する前、編集済みの状態に戻す

### git reset --hard HEAD^

編集前、直前の`git commit`完了時点に戻す (対象ファイルを間違えたとかコミットを間違えただけで編集内容は正しい場合はこれやると事故る)

### --amendの取り消し

`git commit --amend`直後であれば`git reset --soft HEAD@{1}`で元に戻る。  
どこに戻るかは`git reflog`で確認できる。

## resetの取り消し

```
$ git reflog
```

これでresetを含むgitの操作履歴が表示される。
「このコミットの状態に戻したい」のHEAD番号を探して

```
$ git reset --mixed  HEAD@{8}
```

など実行する。
`--mixed`や`--soft`については、最初にresetしたときと同じのを指定すればよいかな？ (未確認)

```
$ git reflog 
de41fb8 (HEAD -> linux) HEAD@{0}: reset: moving to HEAD@{7}
7379519 HEAD@{1}: reset: moving to HEAD@{5}
de41fb8 (HEAD -> linux) HEAD@{2}: reset: moving to HEAD@{5}
de41fb8 (HEAD -> linux) HEAD@{3}: reset: moving to HEAD@{4}
22364c9 HEAD@{4}: reset: moving to HEAD^
de41fb8 (HEAD -> linux) HEAD@{5}: reset: moving to HEAD^
7379519 HEAD@{6}: reset: moving to HEAD
7379519 HEAD@{7}: ....
de41fb8 (HEAD -> linux) HEAD@{8}: ....
```

これで`HEAD@{8}`に`reset`すれば、`de41fb8`をコミットした状態になる。

## 取り消し

### ローカルの変更(未add、未commit)を破棄

```
$ git checkout /path/to/file
```

ファイルシステムから削除したファイルをリポジトリから復旧したい場合も、削除したファイルのパスを指定すればOK  
古いバージョンでないGitであれば、これからは`restore`を使おう。

```console
$ git restore /path/to/file
```

### addの取り消し

```
$ git reset HEAD <path>
```

または

```console
$ git restore --staged <path>
```

ただし、`git init`直後の場合はこの操作は不可。  
`git rm --cached`でバージョン管理から外す操作を行う。

```console
$ git rm --cached <filename>
$ git rm --cached -r <dirname>
```

### mvの取り消し

```
$ git mv foo bar
```

した状態で、無かったことにするには

```
$ git reset HEAD foo
$ git reset HEAD bar
```

の両方を実行する。

※ `git mv`によって、`bar`が作成され、`foo`は削除対象になってるので、それぞれ`reset HEAD`で元に戻す(`bar`は削除して`foo`の`delete`をキャンセル)

memo

```
$ git reset --hard HEAD /path/to/file
fatal: Cannot do hard reset with paths.
```

### mergeの取り消し(conflict)

mergeしようとしたらconflictしてしまい、競合を解消するよりマージ元を改修してマージしなおしたほうが早いような場合。  
マージを取り消すには

```console
$ git merge --abort
```

### rebaseの取り消し(conflict)

`merge`と同様に`--abort`で元に戻せる。

```console
$ git rebase --abort
```

## エラー

### pushでcannot lock ref

リモートリポジトリに`feature`が存在する状態で`feature/foobar`を`push`しようとすると以下のエラー

```
! [remote rejected] feature/foobar -> feature/foobar (cannot lock ref 'refs/heads/feature/foobar': 'refs/heads/feature' exists; cannot create 'refs/heads/feature/foobar')
error: failed to push some refs to 'github.com:...'
```

リモートブランチの削除とローカルの`origin/feature`を削除すればOK
