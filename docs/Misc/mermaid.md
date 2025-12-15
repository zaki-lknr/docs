# Mermaid

## シーケンス図

コード

````text
```mermaid
sequenceDiagram
    participant client
    participant server
    client ->> server: request
    server -->> client: response
```
````

出力は以下

```mermaid
sequenceDiagram
    actor user
    participant client
    participant server
    user ->> client: press button
    client ->> server: request
    server -->> client: response
```

### actor

人型(アクタ)のライフラインを描画(省略時は箱)

`as 〇〇`を付与すれば表示用の文言を設定できる。

````text
```mermaid
sequenceDiagram
    actor yu as Yu Takasaki
    actor ayumu as Ayumu Uehara
    actor kasumi as Kasumi Nakasu
    ayumu ->> yu: 🥰🥰
    yu ->> ayumu: 😅
    kasumi ->> yu: 🫰
    ayumu ->> kasumi: 😡
```
````

```mermaid
sequenceDiagram
    actor yu as Yu Takasaki
    actor ayumu as Ayumu Uehara
    actor kasumi as Kasumi Nakasu
    ayumu ->> yu: 🥰🥰
    yu ->> ayumu: 😅
    kasumi ->> yu: 🫰
    ayumu ->> kasumi: 😡
```

### participant

省略可能。箱型のライフラインを定義。  
また、冒頭部分に記述することで描画の順序を記述準に固定できる。  
`actor`同様、`as`で表示用文言を設定可能。
