# html

## レスポンシブデザイン

ヘッダにこれを入れるのが基本

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

## tag

### meta

#### 文字コード指定

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
```

サーバーの設定をいじれない場合などで。

### form

#### ファイルダイアログの複数選択

```html
<input type="file" multiple/>
```

## css

### 別ファイル読み込み

`@import`を使う。

[@import - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/@import)

### カーソル変更

マウスオーバー時にリンクのスタイルにする

```css
.link_style {
    cursor: pointer;
}
```

このスタイルをブロックに使うと、マウスオーバー時にリンクのスタイルになる。クリックしてjsのコード実行などさせたいときに使える。

選択可能なプロパティ一覧は以下  
[cursor - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/cursor)

### テキスト装飾 (text-decoration)

下線

```css
.text_decoration {
    text-decoration:underline;
}
```
