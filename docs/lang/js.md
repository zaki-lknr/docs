# JavaScript

## 環境・バージョンの対応状況

- [ECMAScript 5 compatibility table](https://compat-table.github.io/compat-table/es5/)
- [ECMAScript 6 compatibility table](https://compat-table.github.io/compat-table/es6/)
- [ECMAScript 2016+ compatibility table](https://compat-table.github.io/compat-table/es2016plus/)

## 実装

### 制御構文

#### if else

```javascript
if (val > 0) {
    console.log('plus');
}
else if (val < 0) {
    console.log('minus');
}
else {
    console.log('else');
}
```

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

### 数値

#### 四捨五入

```javascript
Math.round(double_value);
```

### 文字列

#### 数値から文字への変換

```javascript
String(number_value);
```

#### 文字列から数値への変換

```js
Number(string_value);
```

#### Dateへの変換

コード上単純な変換は、決められた書式になっていればそのままDateインスタンスを作成できる。

```js
date_str = "2024.12.26 23:39:22.12345";
date = new Date(date_str);
console.log(date);
// 2024-12-26T14:39:22.123Z
```

区切り文字がない場合や、24時などは`Invalid Date`となる。

#### 文字列長

文字数は`length`メソッド。

```javascript
const sample_str = '完全にときめいちゃった';
console.log(sample_str.length);
```

出力は`11`

#### 文字列の繰り返し

```js
str = 'abc';
console.log(str.repeat(3));
// 結果は "abcabcabc"
```

#### 部分文字列

##### substring

`substring(開始位置, 終了位置)`を使う。(終了位置は「ここ以降は除外」)

```js
str = 'hello world';
console.log(str.substring(3, 8));
```

出力は`lo wo`

引数が一つの場合は、その位置から最後まで。

```js
console.log(str.substring(3));
// 出力は "lo world"
```

第2引数の負数を指定した場合の動作は以下

- `substring()`は負数あるいは`NaN`が指定されたら`0`として処理
- 第1引数より第2引数が小さい場合は、2つの引数を交換して処理

```js
str = 'hello world';
console.log(str.substring(3, -2));
// "hel"
```

よって、`substring(3, -2)`の処理は、`substring(0, 3)`として処理される。

##### slice

```js
str = 'hello world';
console.log(str.slice(3, 8));
// "lo wo"
```

基本的には`substring()`と同じ

```js
str = 'hello world';
console.log(str.slice(3));
// "lo world"
```

第2引数に負数を指定すると末尾から数える。

```js
str = 'hello world';
console.log(str.slice(3, -2));
// "lo wor"
```

#### 先頭が指定の文字列で始まっているか

```js
val = 'http://www.example.org';

if (val.startsWith('http')) {
    console.log('valid');
}
else {
    console.log('invalid');
}
```

#### split

```js
const string = 'abc,def,ghi';
const items = string.split(",");
```

結果は `[ 'abc', 'def', 'ghi' ]` になる。

[String.prototype.split() - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/String/split)

正規表現を使う場合はそのまま。

```js
strings.split(/,\s*/);
```

#### 文字列長のバイト換算

`string.length`は文字数を返す。  
バイト数を得るには以下。

```javascript
const sample_str = '完全にときめいちゃった';
const size = new Blob([sample_str]).size;
console.log(size);
```

結果は`33`

#### URL文字列のparse

`URL`を使うと簡単

```javascript
const url = 'https://www.swarmapp.com/zaki_hmkc/checkin/66ca6619c1e2dd41311a7dcb?s=UrwNh_CXxd93txmhWtdxCdPOTLQ';
const url_obj = new URL(url);
```

`url_obj`の中身は以下の通り

```javascript
URL {
  href: 'https://www.swarmapp.com/zaki_hmkc/checkin/66ca6619c1e2dd41311a7dcb?s=UrwNh_CXxd93txmhWtdxCdPOTLQ',
  origin: 'https://www.swarmapp.com',
  protocol: 'https:',
  username: '',
  password: '',
  host: 'www.swarmapp.com',
  hostname: 'www.swarmapp.com',
  port: '',
  pathname: '/zaki_hmkc/checkin/66ca6619c1e2dd41311a7dcb',
  search: '?s=UrwNh_CXxd93txmhWtdxCdPOTLQ',
  searchParams: URLSearchParams { 's' => 'UrwNh_CXxd93txmhWtdxCdPOTLQ' },
  hash: ''
}
```

#### URLエンコード

```javascript
encodeURIComponent(string);
```

### 正規表現

[正規表現 - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Regular_expressions)

#### 単純なマッチング(test)

true/falseの判定だけなら(StringでなくRegExpのメソッドの)`test`が使える。  

```js
sample = 'hello world';
if (/world/.test(sample)) {
    console.log("match");
}
```

#### マッチした結果を配列で参照する(match)

```js
date_str = "20241226";

if (r = date_str.match(/^(\d{4})(\d{2})(\d{2})$/)) {
    console.log(r);
}
```

結果は以下の通り

```console
[
  '20241226',
  '2024',
  '12',
  '26',
  index: 0,
  input: '20241226 ',
  groups: undefined
]
```

#### 置換(replace)

```js
const sample_string = '......';
const new_string = sample_string.replace(/regexp/gi, 'replace-string');
```

#### switch文で使う

```js
const str = 'テストしたい文字列';

switch (true) {
    case /^regexp1/.test(str):
        // ...
        break;
    case /^regexp2/.test(str):
        // ...
        break;
    default:
        break;
}
```

### 配列

#### 定義

```js
sample_array = [1, 2, 3];
```

#### 要素の追加

`push`で末尾に追加。

```javascript
const result = [];
for (...) {
    const item = { ... };
    result.push(item);
}
```

#### 要素のクリア

```javascript
const list = [1,2,3,4,5];
list.splice(0);
console.log(list);
console.log(list.length);
```

出力は`[]`と`0`

#### 最後の要素

```js
sample_array = [1, 2, 3];
console.log(sample_array.at(-1));
```

結果は `3`

#### 要素の有無

```js
status = get_http_status();
if ([200, 201, 202, 203].includes(status)) {
    ...
}
```

#### join

```js
sample_array = [1, 2, 3];
console.log(sample_array.join());
```

引数が空の場合は出力は`1,2,3`となる。  
引数を指定すればそれがデリミタとなる。

### 辞書

#### 辞書の定義

```javascript
const data = {
    host: '192.168.0.1',
    user: 'zaki',
    home: '/home/zaki'
}
```

空データ

```javascript
const dict = {};
```

#### 要素の追加

そのまま追加すればOK

```javascript
data.password = 'curry_tabetai';
```

キー部分に記号を含んだり変数参照したい場合は'[]'を使う。

```javascript
data[foo.bar.buz.get('key')] = foo.bar.buz.get('value');
```

#### 空判定

特にそれ用のメソッド等は無い。  
キー一覧を取り出してlengthを見るのがセオリー

```javascript
if (Object.keys(dict).length) { ... }
```

### 関数定義

#### 昔ながらの実装

```javascript
function func(arg) {
    console.log(arg);
}

func('njgk');
```

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

```javascript
let d = new Date();
console.log(d.toISOString());
```

結果(の書式)は`2024-08-17T10:28:28.466Z`

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

#### あるDateオブジェクトの翌日

```javascript
const tomorrow = new Date(foobar_date);
tomorrow.setDate(tomorrow.getDate() + 1);
```

#### タイムゾーンのオフセット取得

```javascript
const d = new Date();
console.log(d.getTimezoneOffset());
console.log(d.getTimezoneOffset() / 60);
```

OSのタイムゾーン設定で決まる。
JST(GMT+0900)設定で実行すると出力は以下の通り。

```console
-540
-9
```

#### UTCの時刻情報を得る

`getFullYear()`などに対応した`getUTCFullYear()`というメソッドを使う。

```javascript
const utc_datestr = d.getUTCFullYear().toString().padStart(4, "0") + "/" + (d.getUTCMonth() + 1).toString().padStart(2, "0") + "/" + d.getUTCDate().toString().padStart(2, "0");
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

### HTTP

#### GET

基本はこれ。

```javascript
const resp = fetch(url);
```

#### レスポンス

text形式でレスポンスbodyを取り出すには

```javascript
const body = resp.text();
```

RESTなどでレスポンスがJSON形式の場合は、オブジェクトとして取り出すこともできる。

```javascript
const obj = resp.json();
```

##### レスポンスコード

```js
if (res.status === 200) {
    // statusが200の場合の処理
}
```

#### リクエストヘッダ

```javascript
const headers = new Headers();
headers.append('accept', 'application/json');

const res = await fetch(url, { headers: headers });
```

#### POST

辞書型データをJSON形式でpostする。

```javascript
const headers = new Headers();
headers.append('Content-Type', 'application/json');
headers.append('Authorization', 'Bearer ' + token);

const payload = JSON.stringify(dict_param);

const res = await fetch(url, { method: "POST", body: payload, headers: headers });
```

### ファイル

#### ブラウザJavaScriptでPC上のファイルを読む

```html
<input type="file" id="file" accept="image/*"/>
<div id="view"></div>
```

```javascript
const file_blob = document.getElementById("file").files[0];
const reader = new FileReader();
reader.readAsDataURL(file_blob);

reader.onload = function() {
    const img = document.createElement("img");
    img.src = reader.result;

    const div = document.getElementById('view');
    div.appendChild(img);
}
```

### 例外

#### throwする

```javascript
const res = await fetch(url, { method: "POST", body: body, headers: headers });
if (!res.ok) {
    throw new Error('fetch failed: ' + await res.text());
}
```

#### catchする

```javascript
try {
    const res_img = await fetch(url);
}
catch(err) {
    throw new Error('http access failed: ' + err + "\nurl: " + url);
}

```

### import

## DOM

### 要素の取得

```javascript
const niji = document.getElementById("niji");
```

これでhtml内の以下の要素が取得できる。

```html
<div id="niji">
```

### 要素の幅・高さ

```javascript
let width  = document.getElementById("niji").clientWidth;
let height = document.getElementById("niji").clientHeight;
```

### 要素別

#### button

```javascript
const btn = document.createElement("button");
btn.textContent = "start";
btn.addEventListener('click', ()=> {
    foobar();
});
```

#### img

```javascript
let image_item = document.createElement("img");
image_item.src = image_url;
```

#### checkbox

```javascript
const checkbox_item = document.createElement("input");
checkbox_item.type = 'checkbox';
checkbox_item.id = 'sample';
checkbox_item.name = 'sample';
checkbox_item.value = 'sample';
checkbox_item.checked = true;
```

チェックボックスのチェック状態を参照するには`.checked`を見ればOK

#### label

```javascript
const label_item = document.createElement("label");
label_item.htmlFor = 'sample';
label_item.textContent = '設定';
```

`id`の指定は`for`でなく`htmlFor`であることに注意。  
[JSでlabel要素にfor属性が追加できない](https://teratail.com/questions/140944)

### 複数の子要素を全て削除する

```javascript
const component = document.getElementById("component");
while(component.firstChild) {
    component.removeChild(component.firstChild);
```

firstChildで子要素が存在する間はその子要素を削除する、をループで回せばOK  
以下のように子要素リストでループしてもうまくいかない。

```javascript
for(const child of component.children) {
    component.removeChild(child);
}
```

### イベントリスナ

#### ページの読み込み後

```javascript
document.addEventListener("DOMContentLoaded", () => {
    // ...
});
```

## html関連

### 画面遷移(動画面)

```javascript
window.location.href = url;
```
