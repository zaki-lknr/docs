# JavaScript

## 実装

### 制御構文

#### for

indexを使った普通のfor文

```js
let items = [1, 2, 3, 4, 5];
for (let i = 0; i < items.length; i++) {
    console.log(items[i]);
}
```

配列処理のforeachタイプは`of`を使う。

```js
for (const item of items) {
    console.log(item);
}
```

#### break

ループを抜ける

#### continue

次のループへ

### 配列

#### 定義

```js
sample_array = [1, 2, 3];
```

#### 最後の要素

```js
sample_array = [1, 2, 3];
console.log(sample_array.at(-1));
```

結果は `3`

### 型の確認

`typeof`を使う。

```js
console.log("type: " + typeof(users))
```

### プロパティの存在チェック

`in`を使う。

```javascript
let data = {
    host: '192.168.0.1',
    user: 'zaki',
    home: '/home/zaki'
}

if ('password' in data) {
    console.log('defined password');
}
else {
    console.log('not defined password');
}
```

これは`not defined`の方が動作する。

### 時刻処理

#### UNIXタイムからDate型

```javascript
// Date()の引数はミリ秒で指定する
unix_date_sec = '1723426784'
let datetime = new Date(unix_date_sec * 1000);

console.log(datetime);
console.log(datetime.toLocaleDateString());
console.log(datetime.toLocaleTimeString());
```

出力は以下

```console
2024-08-12T01:39:44.000Z
2024/8/12
10:39:44
```

### 非同期処理

#### Promise変数

resolve()で中身を取り出す

```javascript
let promise_value = async_func();
const promise = new Promise((resolve) => {
    resolve(promise_value);
}).then((val) => {
    console.log(val);  // Promiseをresolve()したので取れる
})
```

## DOM

### 要素の取得

```javascript
const niji = document.getElementById("niji");
```

これでhtml内の以下の要素が取得できる。

```html
<div id="niji">
```

### 要素別

#### img

```javascript
let image_item = document.createElement("img");
image_item.src = image_url;
```
