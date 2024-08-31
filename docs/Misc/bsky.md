# Bluesky API

## 投稿

投稿の基本は「セッション作成」してセッションの値を使って「投稿」の2回の通信が必要。  
Get Startedにコード例がある。

[Get Started | Bluesky](https://docs.bsky.app/docs/get-started)

### セッション作成

`$BLUESKY_HANDLE`はユーザー名(<your-id>.bsky.socialとか)、`$BLUESKY_PASSWORD`はアプリパスワード

```console
curl -X POST https://bsky.social/xrpc/com.atproto.server.createSession \
    -H "Content-Type: application/json" \
    -d '{"identifier": "'"$BLUESKY_HANDLE"'", "password": "'"$BLUESKY_PASSWORD"'"}'
```

成功すれば、レスポンスのJSONに`accessJwt`というキーがあり、この値が投稿を行う際のベアラトークンとなる。

JavaScriptの場合は以下。

```javascript
    // user_id: ユーザーID
    // password: アプリパスワード
    const url = "https://bsky.social/xrpc/com.atproto.server.createSession";
    const headers = new Headers();
    headers.append('Content-Type', 'application/json');

    const body = JSON.stringify({
        identifier: user_id,
        password: password
    });

    const res = await fetch(url, { method: "POST", body: body, headers: headers });

    const response = await res.json();
    console.log(response.accessJwt);
```

### 投稿

エンドポイントは`com.atproto.repo.createRecord`で、`$ACCESS_JWT`はセッション作成で得た`accessJwt`の値。

```console
curl -X POST https://bsky.social/xrpc/com.atproto.repo.createRecord \
    -H "Authorization: Bearer $ACCESS_JWT" \
    -H "Content-Type: application/json" \
    -d "{\"repo\": \"$BLUESKY_HANDLE\", \"collection\": \"app.bsky.feed.post\", \"record\": {\"text\": \"Hello world! I posted this via the API.\", \"createdAt\": \"$(date -u +%Y-%m-%dT%H:%M:%SZ)\"}}"
```

`createdAt`には実行時のタイムスタンプをセットする必要がある。  
上記サンプルは秒までだが、ミリ秒が入っていてもかまわない。

JavaScriptの場合は以下。

```javascript
    // accessJwt: session id
    // user_id: ユーザーID
    // message: 投稿メッセージ本文
    const url = "https://bsky.social/xrpc/com.atproto.repo.createRecord";
    const headers = new Headers();
    headers.append('Authorization', "Bearer " + accessJwt);
    headers.append('Content-Type', 'application/json');

    let body = JSON.stringify({
        repo: user_id,
        collection: "app.bsky.feed.post",
        record: {
            text: message,
            createdAt: new Date().toISOString(),
        }
    });

    const res = await fetch(url, { method: "POST", body: body, headers: headers });
```

### リンク付き

URL文字列のリンクを有効にするにはバイト数でテキスト位置のリンク部分を指定し、リッチテキストとして投稿する。  
セッションを作成するところは同じで、投稿時のリクエストBodyのパラメタが増える。  
`facets`に指定する位置とURLが本文テキスト中のリンクになる仕様のため、本文にURLがある必要はない。

[Links, mentions, and rich text | Bluesky](https://docs.bsky.app/docs/advanced-guides/post-richtext)

```javascript
let body = JSON.stringify({
    repo: user_id,
    collection: "app.bsky.feed.post",
    record: {
        text: message,
        createdAt: new Date().toISOString(),
        facets = [
            {
                index: {
                    byteStart: ＜message中のリンクURLの開始バイト位置＞,
                    byteEnd: ＜message中のリンクURLの終了バイト位置＞
                },
                features: [{
                    $type: 'app.bsky.richtext.facet#link',
                    uri: ＜URL＞
                }]
            }
        ]
    }
});

await fetch(url, { method: "POST", body: body, headers: headers });
```

### クライアント名(via)

公式にはないパラメタで、一部のクライアントが非公式に使っている。  
`via`に指定する。

```javascript
const body = {
    repo: bsky_id,
    collection: "app.bsky.feed.post",
    record: {
        text: message,
        createdAt: new Date().toISOString(),
        $type: "app.bsky.feed.post",
        via: 'NJGK'
    }
};
```
