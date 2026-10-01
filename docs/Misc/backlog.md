# Backlog

## 書式

- [テキスト整形のルール - よくあるご質問 | Backlog](https://help-center.backlog.com/%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E6%95%B4%E5%BD%A2%E3%81%AE%E3%83%AB%E3%83%BC%E3%83%AB-6a18006f7909d3fe5e860e8e)
- [テキスト整形のルール（Markdown記法） - よくあるご質問 | Backlog](https://help-center.backlog.com/--6a1d4d7f3abb3ada78c5658a)

## ID確認

- プロジェクトID
    - 「プロジェクト設定」のURLのQueryStringで確認(`?projectId=nnnnnn`がついている)

## REST

### プロジェクト一覧

```console
https://${BACKLOG_SPACE}.backlog.com/api/v2/projects?apiKey=${BACKLOG_APIKEY}
```

### プロジェクトメンバー一覧

```console
https://${BACKLOG_SPACE}.backlog.com/api/v2/projects/${PROJECT_KEY}/users?apiKey=${BACKLOG_APIKEY} 
```

[プロジェクトユーザー一覧の取得 | Backlog Developer API | Nulab](https://developer.nulab.com/ja/docs/backlog/api/2/get-project-user-list/)

`${PROJECT_KEY}`はIDでも可

### 課題一覧

[課題一覧の取得 | Backlog Developer API | Nulab](https://developer.nulab.com/ja/docs/backlog/api/2/get-issue-list/)

```console
curl -LO "https://${BACKLOG_SPACE}.backlog.com/api/v2/issues?count=${COUNT}&startDateUntil=${YYYY_mm_dd}&projectId[]=${PROJECT_ID}&statusId[]=1&statusId[]=2&apiKey=${BACKLOG_APIKEY}"
```

### メンション

ユーザーID`12345678`であれば`<@U12345678>`のように記述する。

```js
"(<@U" + user + ">)"
```
