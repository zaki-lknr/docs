# Windows

## 評価版 Windows10

必要事項を入力してダウンロードしたISOは、ライセンスキー入力がなく90日検証に使用できる。

[Microsoft Evaluation Center で Windows 10 Enterprise を試す](https://www.microsoft.com/ja-jp/evalcenter/evaluate-windows-10-enterprise)

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
