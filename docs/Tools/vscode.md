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

| 操作                       | キー                                  |
| ------------------------ | ----------------------------------- |
| 分割したウインドウでウインドウ間カーソル移動   | `Ctrl + k , Ctrl + 移動先画面の方向のカーソルキー` |
| 開いたファイルを分割した別のウインドウで開きたい | `Ctrl + Alt + 左右キー`で隣のウインドウに移動      |

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

## diff

### コマンドラインオプション

```console
$ code -d <file1> <file2>
```

### アクティブなファイルとopen済みファイル

コマンドパレットで`File: Compare Active File with...`を選択し、比較対象を選択する。

## Remote SSH

### git cloneの認証

通常だと認証のプロンプトが表示される操作でこのようになる場合、

```console
Missing or invalid credentials.
Error: connect ECONNREFUSED /run/user/1000/vscode-git-fce9cdb56d.sock
    at PipeConnectWrap.afterConnect [as oncomplete] (net.js:1146:16) {
  errno: -111,
  code: 'ECONNREFUSED',
  syscall: 'connect',
  address: '/run/user/1000/vscode-git-fce9cdb56d.sock'
}
Missing or invalid credentials.
```

アクセスしてるリモート環境へ、[GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)を入れてReload WindowすればVS Codeの画面で認証プロンプトが表示されてアカウント設定できた。

## Remote Container

### ubuntu / focal

#### 起動後の環境

##### git

初めからあるので`git clone`はすぐできる。

##### 鍵設定

作るか持ってくる。  
`~/.ssh/config`も設定。
