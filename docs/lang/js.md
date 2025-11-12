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

#### padStart()で0埋め

> `padStart()`メソッドは、結果の文字列が指定した長さになるように、現在の文字列を他の文字列で（必要に応じて繰り返して）延長します。延長は、現在の文字列の先頭から適用されます。

```js
const d = new Date();
const s = d.getFullYear().toString().padStart(4, "0") + "-"
        + (d.getMonth()+1).toString().padStart(2, "0") + "-"
        + d.getDate().toString().padStart(2, "0") + " "
        + d.getHours().toString().padStart(2, "0") + ":"
        + d.getMinutes().toString().padStart(2, "0") + ":"
        + d.getSeconds().toString().padStart(2, "0");

console.log(s);
```

これで現在時刻の`YYYY-mm-dd HH:MM:ss`形式(0埋め)になる。

<https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/String/padStart>

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

第1引数が負数の場合は末尾から。

```js
str = 'hello world';
console.log(str.slice(-3));
// "rld"
```

第2引数に負数を指定すると末尾から数える。

```js
str = 'hello world';
console.log(str.slice(3, -2));
// "lo wor"
```

第2引数が第1引数より小さい場合は空文字になる

```js
str = 'hello world';
console.log(str.slice(3, 1).length);
// 0
```

##### substr (非推奨)

[String.prototype.substr() - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/String/substr)

> この機能は非推奨になりました。まだ対応しているブラウザーがあるかもしれませんが、すでに関連するウェブ標準から削除されているか、削除の手続き中であるか、互換性のためだけに残されている可能性があります。

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

#### マッチした位置を取得する(search)

`search()`を使うと、最初にマッチした位置を返す。

```javascript
date_str = "date: 20250209";
r = date_str.search(/\d/);
if (r != -1) {
    console.log(r);
}
```

マッチしなかった場合は`-1`が返る。

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

#### map

配列要素に一律に処理をかけるときに使う。  
以下は文字列の配列の各要素末尾に`.`を追加して新しい配列を返す。

```js
const new_array = current_array.map(item => item += ".");
```

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

#### アロー関数

↑の`func(arg){}`定義は以下のように書ける

```js
const func = (arg) => {
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

#### Dateオブジェクトから年月日時分秒を取得

```js
const d = new Date();
console.log(d);
console.log(d.getFullYear());
console.log(d.getMonth() + 1);
console.log(d.getDate());
console.log(d.getHours());
console.log(d.getMinutes());
console.log(d.getSeconds());
```

出力は以下 (`date`は「2025年  2月  8日 土曜日 22:44:42 JST」)

```console
2025-02-08T13:44:42.187Z
2025
2
8
22
44
42
```

#### 日時を表す文字列からDate型

割とアバウトにオブジェクト作成できる。

```js
const str1 = "2024/12/27 11:23:34";
const d1 = new Date(str1);
console.log(d1);
```

出力は以下

```console
2024-12-27T02:23:34.000Z
```

実行した環境のタイムゾーンの時刻として処理される(JSTであれば、日本時間11時23分)  
以下の場合はJSTの12時23分として処理される

```js
const str1 = "2024-12-27T11:23:34+0800";
```

その他に有効な書式例としては以下の通り。

```js
str = "2024-12-27 11:23:34";
str = "2024.12.27 11:23:34";
str = "2024-12-27T11:23:34";
str = "2024-12-27T11:23:34+0900";
```

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

#### Dateオブジェクト同士の差分

Dateインスタンス同士の差分はミリ秒

```js
const today = new Date();
const tomorrow = new Date().setDate(today.getDate() + 1);

console.log(tomorrow - today);
```

出力は86400000

#### タイムゾーンのオフセット取得

```javascript
const d = new Date();
console.log(d.getTimezoneOffset());
console.log(d.getTimezoneOffset() / 60);
```

OSのタイムゾーン設定で決まる。
値は分で得られるためJST(GMT+0900)設定で実行すると出力は以下の通り。

```console
-540
-9
```

#### UTCの時刻情報を得る

`getFullYear()`などに対応した`getUTCFullYear()`というメソッドを使う。

```javascript
const utc_datestr = d.getUTCFullYear().toString().padStart(4, "0") + "/" + (d.getUTCMonth() + 1).toString().padStart(2, "0") + "/" + d.getUTCDate().toString().padStart(2, "0");
```

#### タイムゾーン文字列の取得

```js
const tz = Intl.DateTimeFormat().resolvedOptions().timeZon;
console.log(tz);
```

出力は以下

```console
Asia/Tokyo
```

[Intl.DateTimeFormat.prototype.resolvedOptions() - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/resolvedOptions)

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

#### anchor

```js
const a = document.createElement('a');
a.href = 'https://lovelive-anime.jp/nijigasaki/story.php';
a.textContent = '虹ヶ咲';
a.target = '_blank';
```

#### textfield

```javascript
const field = document.createElement("input")
field.type = 'text';
field.id = 'input_field';
field.value = 'sample';
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

#### select / option

項目1番目の値を参照する。  
(`ver_list.options`の型は`HTMLOptionsCollection`)

```javascript
const select_list = document.getElementById('select_list');
const value = ver_list.options[0].value;
```

全項目をリストとしてループ処理するには`Array.from()`を使ってHTMLOptionsCollectionを配列に変換する。

```javascript
Array.from(select_list.options).find(elem => ... );
```

具体的には、例えば項目内から数値型になってるものをピックアップするには以下

```javascript
Array.from(ver_list.options).find((elem) => Number(elem.value))
```

#### label

```javascript
const label_item = document.createElement("label");
label_item.htmlFor = 'sample';
label_item.textContent = '設定';
```

`id`の指定は`for`でなく`htmlFor`であることに注意。  
[JSでlabel要素にfor属性が追加できない](https://teratail.com/questions/140944)

### メソッド

#### textContent

テキストをセットする。  
書いたとおりに表現されるため、htmlを書いてもタグがそのまま表示される。

以下のように書いても、アンカーとしては機能せず、そのまま`<a href='...'>not anchor text</a>`と表示される。

```javascript
v.textContent = "<a href='...'>not anchor text</a>";
```

#### innerHTML

要素内のHTMLを解釈してテキストをセットする。  
以下のように書けば、アンカーリンクは有効になる。

```javascript
v.innerHTML = "<a href='https://lovelive-anime.jp/nijigasaki/story.php'>link</a>";
```

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

## window関連

### 画面遷移(同画面)

```javascript
window.location.href = url;
```

### 先頭へスクロール

```js
const scroll_to_top = () => {
    window.scroll({
        top: 0,
        behavior: "smooth",
    });
}
```

[add: タイトルバータップで先頭に移動お試し #71 by zaki-lknr · Pull Request #75 · zaki-lknr/swarm-sgbt](https://github.com/zaki-lknr/swarm-sgbt/pull/75)
