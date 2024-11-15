# Windows

## 設定

### スタートアップ

```
%HOME%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```

もしくは `shell:startup` でもアクセスできる。

### ディスプレイ

モニター出力時に余白ができる場合のスケーリングは「インテル グラフィックス・コマンド・センター」を起動し、「ディスプレイ」メニューの「スケール」でスケーリングの設定を見直す。

[ディスプレイの画面の周りが表示されず、黒枠ができる | アイ・オー・データ機器 I-O DATA](https://www.iodata.jp/support/qanda/answer/s30707.htm)

## 評価版 Windows10

必要事項を入力してダウンロードしたISOは、ライセンスキー入力がなく90日検証に使用できる。

[Microsoft Evaluation Center で Windows 10 Enterprise を試す](https://www.microsoft.com/ja-jp/evalcenter/evaluate-windows-10-enterprise)

## network

### ルートテーブルの確認

```
route print -4
```

### スタティックルートの追加

```
route add 192.168.0.0 mask 255.255.255.0 172.16.1.0 -p
```

## disk

### diskpart

パーティション操作

#### 起動

```ps
diskpart
```

#### OEMパーティション削除

「ディスクの管理」では削除できないので`diskpart`で削除する

1. `list disk`でディスクデバイス一覧を確認
1. `select disk *`で処理対象ディスクを選択
1. `list partition`でパーティション一覧を確認
1. `select partition *`で処理対象パーティションを選択
1. `delete partition override`でパーティション削除

```
Microsoft DiskPart バージョン 10.0.19041.964

Copyright (C) Microsoft Corporation.
コンピューター: DESKTOP-NV2IS83

DISKPART> list disk

  ディスク      状態           サイズ   空き   ダイナ GPT
  ###                                          ミック
  ------------  -------------  -------  -------  ---  ---
  ディスク 0    オンライン           476 GB  1024 KB        *
  ディスク 1    オンライン            28 GB    27 GB        *

DISKPART> select disk 1

ディスク 1 が選択されました。

DISKPART> list partition

  Partition ###  Type                Size     Offset
  -------------  ------------------  -------  -------
  Partition 1    プライマリ              224 KB    32 KB
  Partition 2    システム              2880 KB   256 KB
  Partition 3    OEM               1067 MB  3136 KB
  Partition 4    プライマリ              300 KB  1070 MB
```

## 動画

### デスクトップ録画

#### 録画時間の設定

ゲームバーの録画のデフォルトは最長2時間。2時間以上録画するには設定変更が必要。  
「Windowsの設定」->「ゲーム」->「キャプチャ」で、「記録の最大長」を変更する。

試してないけど最長4時間かな。

### 動画の結合

AviUtl使用

1. 1つ目のファイルを「ファイル」「開く」で選択。
2. 2つ目以降のファイルを順に「ファイル」「追加読み込み」
3. 最後に「ファイル」「エクスポート」「MP4 Export」

準備としてmp4を扱うプラグイン(MP4Plugin)をセットアップしておく。

## Terminal

### WindowsTerminal

- 指定ディレクトリをカレントで起動するには`wt -d c:/path/to`
- エクスプローラーから指定パスで起動するには`wt -d .`

## リモートデスクトップ

### 全画面表示時にキーボード操作をローカルに戻す

以下で接続バーにフォーカスが移るのでローカルのデスクトップが操作対象になる。これでAlt-Tabがローカルで効く。

```
Ctrl + Alt + Home
```

ただし再度リモートデスクトップをアクティブにしても接続バーにフォーカスがある状態なので、リモートのデスクトップはフォーカスされない
