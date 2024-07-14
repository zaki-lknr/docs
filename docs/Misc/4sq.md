# Forsquare API

## サイト

- [Foursquare APIs Overview](https://docs.foursquare.com/developer/reference/foursquare-apis-overview)
- [Foursquare Developer Console](https://ja.foursquare.com/developers/home)

## トークン

### アプリの作成

「プロジェクトを作成」を実施。これがアプリとなってClient IDとClient Secretが得られる。  

### APIキー

「Generate API Key」押下で作成。閉じると再表示は不可なのでメモしておく。

## API

### Place Search

```console
curl --request GET \
     --url https://api.foursquare.com/v3/places/search \
     --header "Authorization: FSQ_APP_API_KEY" \
     --header 'accept: application/json'
```

緯度経度・検索文字列追加

```console
curl --request GET \
     --url 'https://api.foursquare.com/v3/places/search?query=%E3%81%8A%E5%8F%B0%E5%A0%B4&ll=35.6325714%2C139.7738516' \
     --header "Authorization: $FSQ_APP_API_KEY" \
     --header "accept: application/json"
```
