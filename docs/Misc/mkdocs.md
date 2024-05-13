# MkDocs

- [Configuration - MkDocs](https://www.mkdocs.org/user-guide/configuration/)
- [Getting started - Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/)
- [Deploying Your Docs - MkDocs](https://mkdocs.readthedocs.io/en/latest/user-guide/deploying-your-docs/)
- [プロジェクトドキュメント構築向け静的サイトジェネレータ『MkDocs』及び『Material for MkDocs』の個人的導入＆設定まとめ | DevelopersIO](https://dev.classmethod.jp/articles/mkdocs-and-material-for-mkdocs-tips-matome/#custom-theme)

## GitHub Pages設定

### 手動実行してGitHub Pages公開

ソースmarkdown用リポジトリは既に存在し、リモートリポジトリに設定されている状態で

```console
$ mkdocs gh-deploy --clean
INFO    -  Cleaning site directory 
INFO    -  Building documentation to directory: /home/zaki/src/github-pages/site 
INFO    -  Documentation built in 0.19 seconds 
WARNING -  Version check skipped: No version specified in previous deployment. 
INFO    -  Copying '/home/zaki/src/github-pages/site' to 'gh-pages' branch and pushing to GitHub. 
INFO    -  Your documentation should shortly be available at: https://zaki-lknr.github.io/pages/ 
```

リモートリポジトリに`gh-pages`ブランチが作成され、ここにビルドされたコンテンツがpushされて以下のURLにコンテンツが公開された。  
ディレクトリ名はリポジトリ名になっている。

https://zaki-lknr.github.io/pages/

### GitHub Actions連携

[MkDocs で作ったドキュメントを GitHub Actions を使って GitHub Pages にデプロイしよう | ultra code](https://futureys.tokyo/lets-deploy-document-built-by-mkdocs-to-github-pages-by-using-github-actions/)

- 以下を用意
    - ソースmarkdown用リポジトリ
        - リポジトリ名は任意
    - GitHub Pages公開コンテンツ用リポジトリ
        - このリポジトリ名は`<GitHubユーザー名>.github.io`にする

### GitHub Pagesはドキュメントルートで公開

公開用リポジトリ名を[<GitHubユーザー名>.github.io](https://github.com/zaki-lknr/zaki-lknr.github.io)にすればOKだった。  
詳細は未確認。

[Github pagesで意地でもサブディレクトリをルートにする | Matsuura Tomoya|松浦知也](https://matsuuratomoya.com/blog/2016-05-07/githubpage-subdirectory/)

## コンテンツ設定

### 色設定

- [Changing the colors - Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/)
- [MKDocs – 基本のドキュメント配色設定方法 | DevelopersIO](https://dev.classmethod.jp/articles/mkdocs-color-palette/)

### コードハイライト

[MKDocs – 拡張プラグイン集(1) | DevelopersIO](https://dev.classmethod.jp/articles/mkdocs-plugins-1/#toc-9)

あとで公式もチェックするか。。

この辺かな:  
[Code blocks - Material for MkDocs](https://squidfunk.github.io/mkdocs-material/reference/code-blocks/)

### 日本語検索

materialテーマの場合

[Material for MkDocs を日本語対応した話＆初めてのOSSコントリビュート - miyalog](https://miyalog.hatenablog.jp/entry/2017-11-07_MkDocs_Material_Japanese_support)

### Google Analytics設定

[Setting up site analytics - Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/setting-up-site-analytics/)

```yaml
google_analytics:
  - UA-XXXXXXXX-X
  - auto
```

### 取り消し線の有効化

Material for MkDocsを使っていると(?)、`~~`による取り消し線が動作しない。  
以下設定を追加すると有効になる。

```yaml
markdown_extensions:
    - pymdownx.tilde
```
