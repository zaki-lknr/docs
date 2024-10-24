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

### カーソル変更

マウスオーバー時にリンクのスタイルにする

```css
.link_style {
    cursor: pointer;
}
```

このスタイルをブロックに使うと、マウスオーバー時にリンクのスタイルになる。クリックしてjsのコード実行などさせたいときに使える。

選択可能なプロパティ一覧は以下  
[CSS3で使える35+α種類のカーソルの一覧表。（CSS おれおれ Advent Calendar 2012 – 07日目） | Ginpen.com](https://ginpen.com/2012/12/08/css-cursors/)
