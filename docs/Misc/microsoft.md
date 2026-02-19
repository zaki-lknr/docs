# Microsoft

## Office

### Word

#### 表作成時の表番号自動入力

「参考資料」->「図表番号の挿入」->「自動設定」で「図表番号の自動設定」ダイアログで「Microsoft Wordの表」にチェックを入れる。  
表番号の位置はオプションで上下を選択する。

テキストのセンタリング等は「スタイルの変更」で設定する。

#### 図番号の挿入

「参考資料」->「図表番号の挿入」

## Edge Browser

### コマンドラインからの起動

実行ファイルは以下

```
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe"
```

ただパスが通ってないので、`msedge`を実行しても起動はしない。

パス指定するかフルパス指定しても起動するけど、Edge起動のための`microsoft-edge:`スキームが用意されてるので、これを使って起動できる。

```
PS > start microsoft-edge:https://www.google.com
```

[Windows 10ミニTips(86) Microsoft Edgeをコマンドラインから起動する | マイナビニュース](https://news.mynavi.jp/article/win10tips-86/)

## 機種変更時のAuthenticatorアプリ

- 旧端末のAuthenticatorアプリでメニューの「サインインしてバックアップを更新する」を実行
- 「設定」のバックアップの詳細で、バックアップされているか日付を確認
- 新端末にAuthenticatorをインストール、初期セットアップ内で「バックアップから復元」実行
- 認証時に旧端末で承認操作があるので続行
