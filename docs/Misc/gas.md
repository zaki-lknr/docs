# Google Apps Script

## Spreadsheet

### シートの読み込み

```javascript
var ss = SpreadsheetApp.getActiveSpreadsheet();
var sheet = ss.getSheetByName("シート1");
```

### セルにアクセス

```javascript
// 参照
var val = sheet.getRange("B10").getValue();

// 更新
sheet.getRange("B10").setValue("ここはB10")
```

## HTTP通信

`UrlFetchApp.fetch()`を使う。

### Get

```javascript
const resp = UrlFetchApp.fetch(url);
```

## UI

### ダイアログ

```javascript
const ui = SpreadsheetApp.getUi();
const result = ui.alert(message_string);
```

### テキスト入力 (prompt)

```javascript
const ui = SpreadsheetApp.getUi();
const result = ui.prompt(title, msg, ui.ButtonSet.YES_NO);
const button = result.getSelectedButton();
const input = result.getResponseText();

if (button == ui.Button.YES) {
    // OK押下の処理
    Logger.log("input text: " + input);
}
```

## プロパティ

[プロパティ サービス  |  Apps Script  |  Google for Developers](https://developers.google.com/apps-script/guides/properties?hl=ja)

ハードコードしたくないパラメタ類はプロパティを使って外部入力可能。いくつか種類がある。

- スクリプトプロパティ
- ユーザープロパティ
- ドキュメントプロパティ
