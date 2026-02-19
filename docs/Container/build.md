# イメージビルド

## ベースイメージ

```Dockerfile
FROM debian:bookworm-slim
```

- Debian Slim: `stable-slim`, `13.3-slim`, `trixie-slim` など

軽量化を目的だけにAlpineを使うのは注意。

- [軽量Dockerイメージに安易にAlpineを使うのはやめたほうがいいという話 - inductor's blog](https://blog.inductor.me/entry/alpine-not-recommended)
- [とりあえずでDockerイメージにAlpine Linuxを選択するのはやめましょうという話 - NIFTY engineering](https://engineering.nifty.co.jp/blog/26586)

## ロケールの追加

Debian系の場合はこんな感じ。

```Dockerfile
FROM guacamole/guacamole:1.4.0

USER 0
RUN apt-get update \
    && apt-get install -y locales \
    && sed -i -E 's/# (ja_JP.UTF-8)/\1/' /etc/locale.gen \
    && locale-gen \
    && update-locale LANG=ja_JP.UTF-8
ENV LANG ja_JP.UTF-8

USER 1001
```

## ファイルコピー(COPY)

```Dockerfile
COPY script.py /usr/local/src
CMD python /usr/local/src/script.py
```

ビルドするホストのカレントにある`script.py`をコンテナの`/usr/local/src/script.py`へコピーする

## 環境変数(ENV / ARG)

`ENV`と`ARG`はどちらも環境変数を定義する。

```Dockerfile
ENV APP_ID=1234567890
```

`ENV`と`ARG`の違いは、

- ビルドされたイメージにも`ENV`で指定した環境変数は残る。`ARG`はビルド中のみ
- `ARG`はビルド時に`--build-arg`で上書きできる

## ビルドキャッシュの削除

```console
docker builder prune
```
