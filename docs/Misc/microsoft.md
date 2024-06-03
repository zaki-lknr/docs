# Microsoft

## Office

### Word

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
