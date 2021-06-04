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

## 過去のコミットに対する打ち消しコミット

コミットログ上は「過去コミット」「打ち消しコミット」が残るもの

```
$ git revert <取り消したいコミットID>
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

## 取り消し

### ローカルの変更(未add、未commit)を破棄

```
$ git checkout /path/to/file
```

### addの取り消し

```
$ git reset HEAD <path>
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
