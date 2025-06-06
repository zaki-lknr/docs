# VS Code

## コマンド

`Ctrl + Shift + p`でコマンドパレットを起動

| コマンド                      | 機能                     |
| ------------------------- | ---------------------- |
| Developer: Reload Windows | ウインドウの再起動(拡張等の再読み込み含む) |

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
- Draw.io
    - [Draw.io Integration - Visual Studio Marketplace](https://marketplace.visualstudio.com/items/?itemName=hediet.vscode-drawio)
        - `.drawio`を開いたときにDraw.ioエディタを起動する
- 補助
    - [Bracket Pair Colorizer 2 - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)
        - 括弧の開始終了間の補助線表示
    - [indent-rainbow - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)
        - インデントレベル毎にスペース部分をハイライト
    - [TODO Highlight - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight)
        - ファイル中テキストの`TODO:`とか`FIXME:`をハイライトしてくれる
- テーマ系
    - [GitHub Theme - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=GitHub.github-vscode-theme)
    - [Material Icon Theme - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)

## 設定

自動同期設定してるから個別に設定することなくなった…けど、クローズドなPCの設定用に。

| item                     | value      | description          |
| ------------------------ | ---------- | -------------------- |
| files.autoSave           | afterDelay | 自動保存                 |
| files.autoGuessEncoding  | true       | ファイルを開くときのエンコードを自動判別 |
| editor.mouseWheelZoom    | true       | Ctrl+wheelでフォントサイズ変更 |
| editor.renderWhitespace  | all        | スペースやtabの可視化         |
| files.insertFinalNewline | true       | ファイル末尾に改行を付ける        |

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

### ターミナルのシェルをcygwinのzshにする

`chere`パッケージを入れて以下の設定を追加する。

```json
    "terminal.integrated.shell.windows": "C:\\cygwin64\\bin\\bash.exe",
    "terminal.integrated.shellArgs.windows": ["/bin/xhere", "/bin/zsh"],
```

### ターミナルをGit Bashをデフォルトにする

```json
    "terminal.integrated.defaultProfile.windows": "Git Bash",
```

### allowedUNCHosts

CIFS上のファイルを開こうとしたときにエラーになる場合「Allowed UNCHosts」に開きたいサーバーを加え、VSCodeを再起動する。  
settings.jsonに直接記載する場合は以下の通り。

```json
    "security.allowedUNCHosts": [
        "file-server"
    ],
```

## ショートカット

※ windows前提

### エディタ

| 操作                     | キー                                                        |
| ---------------------- | --------------------------------------------------------- |
| 分割したウインドウでウインドウ間カーソル移動 | `Ctrl + k , Ctrl + 移動先画面の方向のカーソルキー` (`Ctrl + NUM`の方が速いかも) |
| 表示中のファイルを別ウインドウで開く     | `Ctrl + k, o`                                             |
| 表示中ファイルを分割した別のウインドウで開く | `Ctrl + Alt + 左右キー`で隣のウインドウに移動                            |
| 表示中ファイルを左右両方で分割オープン    | `Ctrl + K, Ctrl + Shift + \` (トグルなので再実行で解除)               |
| 開いているファイル一覧            | `Ctrl + k, Ctrl + p`                                      |
| 全てのエディタを閉じる            | `Ctrl + k, w` (未保存のファイルがあると保留)                            |
| 全ての保存済みエディタを閉じる        | `Ctrl + k, u`                                             |

#### マルチカーソル

| 操作               | キー                   |
| ---------------- | -------------------- |
| カーソル上下移動と同時に起点追加 | `Ctrl + Alt + ↑/↓` |
| 任意の位置へ起点追加       | `Alt + マウスクリック`      |

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
