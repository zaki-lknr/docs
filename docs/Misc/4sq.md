# Forsquare API

## サイト

- [Foursquare APIs Overview](https://docs.foursquare.com/developer/reference/foursquare-apis-overview)
- [Foursquare Developer Console](https://ja.foursquare.com/developers/home)

## トークン

### アプリの作成

「プロジェクトを作成」を実施。これがアプリとなってClient IDとClient Secretが得られる。  

### APIキー

「Generate API Key」押下で作成。閉じると再表示は不可なのでメモしておく。

### OAuth(手動)

[Authentication](https://docs.foursquare.com/developer/reference/personalization-apis-authentication)

- トークンがqueryとしてログに記録されても困らないwebサーバーを用意して「Redirect URL」に設定
- `https://foursquare.com/oauth2/authenticate?client_id=YOUR_CLIENT_ID&response_type=code&redirect_uri=YOUR_REGISTERED_REDIRECT_URI`にブラウザでアクセスして「許可」
- リダイレクト先URLに遷移して、そのときのURL末尾の`code=***`をメモ
- `https://foursquare.com/oauth2/access_token?client_id=YOUR_CLIENT_ID&client_secret=YOUR_CLIENT_SECRET&grant_type=authorization_code&redirect_uri=YOUR_REGISTERED_REDIRECT_URI&code=CODE`にアクセス

これでtokenが表示される

## API

### Place Search

[Place Search](https://docs.foursquare.com/developer/reference/place-search)

```console
curl --request GET \
     --url https://api.foursquare.com/v3/places/search \
     --header "Authorization: $FSQ_APP_API_KEY" \
     --header 'accept: application/json'
```

緯度経度・検索文字列追加

```console
curl --request GET \
     --url 'https://api.foursquare.com/v3/places/search?query=%E3%81%8A%E5%8F%B0%E5%A0%B4&ll=35.6325714%2C139.7738516' \
     --header "Authorization: $FSQ_APP_API_KEY" \
     --header "accept: application/json"
```

検索結果件数の指定(デフォルト10 / MAX 50)

```console
--url "https://api.foursquare.com/v3/places/search?query=${query}&ll=${position}&limit=50"
```

### Get User Detail

[Get User Details](https://docs.foursquare.com/developer/reference/get-user-details)

```console
curl --request GET \
     --url "https://api.foursquare.com/v2/users/self?v=20240109&oauth_token=$FSQ_OAUTH_TOKEN" \
     --header "accept: application/json"
```

任意のユーザーの情報であれば以下。

```console
curl --request GET \
     --url "https://api.foursquare.com/v2/users/${USER_ID}?v=20240109&oauth_token=$FSQ_OAUTH_TOKEN" \
     --header "accept: application/json"
```

### Get User Checkins

[Get User Checkins](https://docs.foursquare.com/developer/reference/get-user-checkins)

```console
curl --request GET \
     --url "https://api.foursquare.com/v2/users/self/checkins?v=20231010&limit=5&offset=0&oauth_token=$FSQ_OAUTH_TOKEN" \
     --header 'accept: application/json'
```

`offset`から`limit`件数分のチェックイン一覧。  
`offset`はデフォルト`0`で制限無し(初回チェックインを超過するとどうなるかは不明)、`limit`はデフォルト`20`で最大`250`まで指定可。  
`limit: 20`の場合の2ページ目は、`offset: 20`を指定する(ページ番号ではなく"limit * ページ番号"になる)。

レスポンスの基本的な構造は以下の通り。(`shout`はコメントが無ければキーも無い)

```json
{
    "response": {
        "checkins": {
            "count": 総チェックイン数,
            "items": [
                {
                    "id": "チェックインID",
                    "shout": "コメント",
                    ...
                    "venue": {
                        "id": "ヴェニューID",
                        "name": "ヴェニュー名",
                        ...
                    },
                    "photos": {
                        ...
                    }
                }
}
```

このチェックインIDでdetailsを取得する。

#### photos

photosに関しては以下。

```json
"photos": {
    "count": 枚数,
    "items": [
        {
            "id": "...",
            "prefix": "https://fastly.4sqi.net/img/general/",
            "suffix": "/.........jpg",
            "width": width,
            "height": height,
        }
    ]
}
```

このときの画像のリンクは `prefix + width + "x" + height + suffix` でアクセスできる。  
(厳密にはwidth x heightは指定した値に拡縮・トリミングされる)

#### location

旧シェア文字列のアドレスの「港区, 東京都」は `venue.location.formattedAddress` に配列要素の一つとしてセットされている。

```json
"venue": {
    "location": {
        "address": "台場一丁目",
        "postalCode": "135-0091",
        "cc": "JP",
        "city": "東京",
        "state": "東京都",
        "country": "日本",
        "formattedAddress": [
            "台場一丁目",
            "港区, 東京都",
            "135-0091"
        ]
    },
```

ただし、`location`の要素の`postalCode`が無い場合は3つ目の"135-0091"相当が、`address`が無い場合は1つ目の"台場一丁目"が無いため、配列要素は固定ではない。  
(2番目の"港区, 東京都"が無いパターンの有無は不明)

また、`formattedAddress`自体が無いヴェニューもある。

### Get Check-in Details

CheckinsでチェックインのIDが取れたらDetailsでシェア用URLなども取れる

```console
curl --request GET \
     --url "https://api.foursquare.com/v2/checkins/$CHECKIN_ID?v=20231010&oauth_token=$FSQ_OAUTH_TOKEN" \
     --header 'accept: application/json'
```

このレスポンスにアクセス可能な`response.checkin.checkinShortUrl`がある。  
Twitterなどのシェアを有効にしていると`shares`フィールドが追加される。  
レスポンスの基本的な構造は以下の通り。

```json
{
  "response": {
    "checkin": {
      "id": "チェックインID",
      "shout": "233日記念日",
      :
      ,
      "user": {
      },
      "venue": {
      },
      "photos": {
      },
      "checkinShortUrl": "https://www.swarmapp.com/.../checkin/...",
      "shares": {
        "twitter": true
      }
    }
  }
}
```

### Get Place Details

APIはv2がすでに無くなりv3を使う必要がある。  
[Get Place Details](https://docs.foursquare.com/developer/reference/place-details)

```console
curl --request GET \
     --url "https://api.foursquare.com/v3/places/${PLACE_ID}?fields=social_media" \
     --header "Authorization: ${FSQ_APP_API_KEY}" \
     --header "accept: application/json"
```

`?fields=`で返すフィールドを指定できる。  
TwitterIDなどのソーシャルメディア情報であれば`?fields=social_media`を指定。

```json
{
  "social_media": {
    "twitter": "3rdhakatadaruma"
  }
}
```
