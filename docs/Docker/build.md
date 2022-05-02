# イメージビルド

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
