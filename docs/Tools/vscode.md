# VS Code

## 拡張

- Markdown系
    - [Markdown PDF - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf)
    - [markdownlint - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)
    - [Text Tables - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=RomanPeshkov.vscode-text-tables)
    - [Paste Image - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image)
        - Markdownファイルを開いている状態でクリップボードの画像とかを`Ctrl-Alt-V`でペーストすることでサブディレクトリ以下にファイル生成 & 画像表示のタグが挿入される
- 設定系
    - [Modelines - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=chrislajoie.vscode-modelines)
        - ソースの先頭部分に `# -*- mode: hogehoeg -*-` って書いた内容が有効になる
- 補助
    - [Bracket Pair Colorizer 2 - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)
        - 括弧の開始終了間の補助線表示
    - [indent-rainbow - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)
        - インデントレベル毎にスペース部分をハイライト
    - [TODO Highlight - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight)
        - ファイル中テキストの`TODO:`とか`FIXME:`をハイライトしてくれる

## 設定

自動同期設定してるから個別に設定することなくなった…けど、クローズドなPCの設定用に。

|item|value|description|
|---|---|---|
|files.autoSave|afterDelay|自動保存|
|files.autoGuessEncoding|true|ファイルを開くときのエンコードを自動判別|
|editor.mouseWheelZoom|true|Ctrl+wheelでフォントサイズ変更|
|editor.renderWhitespace|all|スペースやtabの可視化|
|files.insertFinalNewline|true|ファイル末尾に改行を付ける|

```json
    "files.insertFinalNewline": true,
    "files.autoGuessEncoding": true,
    "editor.renderControlCharacters": true,
    "editor.renderWhitespace": "all",
    "files.autoSave": "afterDelay",

    "editor.suggestSelection": "first",

    "editor.wordSeparators": "`~!@#$%^&*()-=+[{]}\\|;:'\",.<>/?、。　「」『』【】（）",

    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "window.zoomLevel": 0,
    "editor.mouseWheelZoom": true,

    "terminal.integrated.scrollback": 100000,

    "markdownlint.config": {
        "MD025": false,              // 見出しlevel1は文書中に一つ:off
        "MD007": { "indent": 4 },    // リストのインデント量
        "MD040": false,              // コードブロックの言語指定
    },

    "files.watcherExclude": {
        "**/venv*/**": true,
        "**/env*/**": true,
    },

```

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