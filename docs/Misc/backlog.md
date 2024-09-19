# Backlog

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

`${PROJECT_KEY}`はIDでも可

### 課題一覧

[課題一覧の取得 | Backlog Developer API | Nulab](https://developer.nulab.com/ja/docs/backlog/api/2/get-issue-list/)

```console
curl -LO "https://${BACKLOG_SPACE}.backlog.com/api/v2/issues?count=${COUNT}&startDateUntil=${YYYY_mm_dd}&projectId[]=${PROJECT_ID}&statusId[]=1&statusId[]=2&apiKey=${BACKLOG_APIKEY}"
```
