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

### 型の確認

`typeof`を使う。

```js
console.log("type: " + typeof(users))
```
