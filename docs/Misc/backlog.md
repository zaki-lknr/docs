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
