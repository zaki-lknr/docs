# VS Code

## 拡張

- [Markdown PDF - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf)
- [markdownlint - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)
- [Text Tables - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=RomanPeshkov.vscode-text-tables)
- [Modelines - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=chrislajoie.vscode-modelines)
    - ソースの先頭部分に `# -*- mode: hogehoeg -*-` って書いた内容が有効になる

## ショートカット

※ windows前提

### エディタ

|操作|キー|
|---|---|
|分割したウインドウでウインドウ間カーソル移動|`Ctrl + k , Ctrl + 移動先画面の方向のカーソルキー`|

### エクスプローラ

|操作|キー|
|---|---|
|エクスプローラで開く|`Shift + Alt + R`|

### ターミナル操作

|操作|キー|
|---|---|
|ターミナルの表示/非表示|`Ctrl + @`|
|ターミナルを開いたままエディタにフォーカス移動|`Ctrl + 1` (正確にはエディタグループ1に移動)|
|新しいターミナルの作成|`Ctrl + Shift + @`|
|左右2分割したターミナルで左右にフォーカスを行き来する|`Alt + ←` or `Alt + →`|
|ターミナルペインの最大化|ショートカット無し / コマンドパレットで`View: Toggle Maxmized Panel`|

---

## キーバインド設定

ターミナルのキーバインド

```json
// 既定値を上書きするには、このファイル内にキー バインドを挿入します
[
    {
        "key": "ctrl+a",
        "command": "cursorHome",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+e",
        "command": "cursorEnd",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+p",
        "command": "cursorUp",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+n",
        "command": "cursorDown",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+f",
        "command": "cursorRight",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+b",
        "command": "cursorLeft",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+alt+right",
        "command": "workbench.action.terminal.focusNext",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+alt+left",
        "command": "workbench.action.terminal.focusPrevious",
        "when": "terminalFocus"
    },
]
```

terminalでCtrl-kが次コマンド待ちになってしまう場合は以下も追加

```json
{
  "key": "ctrl+k",
  "command": "deleteAllRight",
  "when": "terminalFocus"
}
```

## Remote Container

### ubuntu / focal

#### 起動後の環境

##### git

初めからあるので`git clone`はすぐできる。

##### 鍵設定

作るか持ってくる。  
`~/.ssh/config`も設定。
