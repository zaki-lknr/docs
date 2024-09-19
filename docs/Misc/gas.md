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
